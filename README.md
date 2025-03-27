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

**❗ Desativando Transparent Huge Pages (THP)l**

`cat /sys/kernel/mm/transparent_hugepage/enabled`
`sudo vi /etc/default/grub`
`# Adicionar: transparent_hugepage=never`
`sudo grub2-mkconfig -o /boot/grub2/grub.cfg`
`sudo reboot`



