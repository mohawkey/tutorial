# Container MSCManager
## Create Container
~~~
$ lxc launch ubuntu:22.04 mcsmanager -n br0
~~~
## Access Container
~~~
$ lxc exec mcsmanager bash
# apt update && apt upgrade -y
~~~
## Config ip-address
~~~
# rm /etc/netplan/*
# nano /etc/netplan/network.yaml
~~~
~~~yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
      addresses: [192.168.1.155/24]
      routes:
         - to: default
           via: 192.168.1.1
      nameservers:
        addresses: [192.168.1.151, 1.1.1.1]
~~~ 
## set permissions
~~~
chmod 600 /etc/netplan/network.yaml

netpla generate
netplan apply 
~~~
## Install mscmanager
~~~
sudo su -c "wget -qO- https://script.mcsmanager.com/setup.sh | bash"

# Restart panel command  
systemctl start mcsm-daemon.service
systemctl enable mcsm-daemon.service

systemctl start mcsm-web.service
systemctl enable mcsm-web.service

# apt install openjdk-21-jre-headless

mkdir /srv/mcs-main && cd /srv/main
echo "eula=true" > eula.txt
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTExMTI0Mzc2OSw5NDAzNDAxMjcsMzM1OT
QzNzI2LC02MzM0OTI5NzgsLTE5ODk3NjI5OTgsMjAyNDUyODMy
MSwtMjAyODk5ODI1NV19
-->