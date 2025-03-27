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


