# Container MSCManager
## Create Container
~~~
$ lxc launch ubuntu:22.04 mscmanager-155 -n br0
~~~
## Config ip-address
~~~
# sudo nano /etc/netplan/network.yaml
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
chmod 600 network.yaml
apply netplan
~~~
## Install mscmanager
~~~
sudo su -c "wget -qO- https://script.mcsmanager.com/setup.sh | bash"

# Restart panel command  
systemctl start mcsm-daemon.service  
systemctl start mcsm-web.service

sudo apt install openjdk-21-jre-headless

echo "eula=true" > eula.txt
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5ODk3NjI5OTgsMjAyNDUyODMyMSwtMj
AyODk5ODI1NV19
-->