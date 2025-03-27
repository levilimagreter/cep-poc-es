# Instalação e Integração do Splunk ES 8 no Linux

Este guia cobre a instalação do Splunk Enterprise, integração com ES 8.0.2, configuração de logs Cisco ASA e Carbon Black EDR, bem como simulações e ajustes de sistema.

**❗ Regras de Firewall**

`sudo iptables -I INPUT -p tcp --dport 8000 -j ACCEPT`

`sudo iptables -I INPUT -p tcp --dport 8443 -j ACCEPT`

`sudo iptables -I INPUT -p tcp --dport 443 -j ACCEPT`

`sudo iptables -I INPUT -p tcp --dport 8088 -j ACCEPT`

`sudo iptables -I INPUT -p tcp --dport 9997 -j ACCEPT`

`sudo iptables-save | sudo tee /etc/iptables.rules`

`sudo bash -c "echo -e '#!/bin/sh\n/sbin/iptables-restore < /etc/iptables.rules' > /etc/network/if-pre-up.d/iptables"`

`sudo chmod +x /etc/network/if-pre-up.d/iptables`

`sudo iptables -L -n`

**❗ Desativando Transparent Huge Pages (THP)**

`cat /sys/kernel/mm/transparent_hugepage/enabled`

`sudo vi /etc/default/grub`

`# Adicionar: transparent_hugepage=never`

`sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

`sudo reboot`

**🔧 Instalação do Splunk Enterprise**

`ssh usuario@<ip_servidor>`

`sudo useradd -m -r splunkuser`

`sudo passwd splunkuser`

`sudo usermod -aG sudo splunkuser`

`sudo chsh -s /bin/bash splunkuser`

`su - splunkuser`

`sudo wget -O splunk-9.4.1.tgz https://download.splunk.com/products/splunk/releases/9.4.1/linux/splunk-9.4.1-e3bdab203ac8-linux-amd64.tgz`

`sudo mkdir /opt/splunk`

`sudo chown -R splunkuser:splunkuser /opt/splunk`

`tar -xzvf splunk-9.4.1.tgz -C /opt`

`/opt/splunk/bin/splunk start --accept-license`

`sudo /opt/splunk/bin/splunk enable boot-start -user splunkuser --accept-license`

**🔧 Criar Indexes**

`/opt/splunk/bin/splunk add index network -datatype event`

`/opt/splunk/bin/splunk add index edr -datatype event`

**🔧 Configurar inputs.conf**

**Cisco ASA**

`[monitor:///var/log/splunk_real_env/cisco_firewall.log]`

`index = network
sourcetype = cisco:asa
disabled = false`

**Carbon Black**

`[monitor:///var/log/splunk_real_env/carbon_black_edr.log]`

`index = edr
sourcetype = carbonblack:edr
disabled = false`

**🔧 Scripts de Geração de Logs**

Scripts Python para gerar eventos Cisco ASA e Carbon Black estão localizados em:

`/var/log/splunk_real_env/generate_cisco_asa_logs.py`

`/var/log/splunk_real_env/generate_carbon_black_edr_logs.py`

Executar com:

`sudo nohup python3 caminho/script.py > /dev/null 2>&1 &`

**🔧 Configurar macros.conf**

`[Network_Traffic_Indexes]
definition = (index=network OR index=main)`

`[Endpoint_Indexes]
definition = (index=edr OR index=main)`

**🔧 Testar com tstats**

`| tstats count FROM datamodel=Network_Traffic.All_Traffic WHERE index=network BY _time, All_Traffic.src, All_Traffic.dest`

`| tstats count FROM datamodel=Endpoint.Processes WHERE index=edr BY _time, Processes.process_name`

**🧪 Transferir e instalar o Splunk ES**

`scp splunk-enterprise-security_802.spl splunkuser@<ip>:/home/splunkuser/`

`sudo chmod +x splunk-enterprise-security_802.spl`

`sudo /opt/splunk/bin/splunk install app /home/splunkuser/splunk-enterprise-security_802.spl -auth admin:splunkuser`

**🧪 Ajustar Timeout Splunk Web**

`# /opt/splunk/etc/system/local/web.conf`

`[settings]
startwebserver = true
splunkdConnectionTimeout = 300`

`# /opt/splunk/etc/splunk-launch.conf`

`SPLUNKD_CONNECTION_TIMEOUT=300`

**🧪 Simular SQL Injection**

Script: 

`/var/log/splunk_real_env/sql_injection_simulation.py`

Inclui tentativa de SQL Injection e logs formatados como:

`[INFO] 2025-03-12 12:10:00 src_ip=192.168.1.10 dest_ip=10.0.0.5 sql_injection=' OR 1=1 --'`

Rodar:

`sudo nohup python3 /var/log/splunk_real_env/sql_injection_simulation.py > /dev/null 2>&1 &`

**✅ Ativar Regra de Correlaçãon**

Vá para:

ES Menu > Configure > Content Management

Filtre por "Correlation Searches"

Ative e edite conforme necessidade (escolha index, tags, notáveis, etc)

**Pronto! Agora seu ambiente Splunk ES está preparado com logs simulados de firewall e EDR, mapeados para o CIM, prontos para dashboards, notáveis e investigações.**

