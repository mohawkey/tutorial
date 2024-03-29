# Ubuntu Server 22.04.4 LTS
## Main Server Installation
### Server Installation
Update & Package Installation
~~~
# sudo apt-get update
# sudo apt-get upgrade
# sudo apt-get install -y nano net-tools zfsutils dnsutils
~~~
### Configuring Static IP and Bridged Network
~~~
# sudo nano /etc/netplan/network.yaml
~~~
~~~
network:
  version: 2
  renderer: networkd

  ethernets:
    enp1s0:
       dhcp4: false
       dhcp6: false

  bridges:
    br0:
       interfaces: [enp1s0]
       addresses: [192.168.1.150/24]
       routes:
          - to: default
            via: 192.168.1.1
       mtu: 1500
       nameservers:
          addresses: [192.168.1.151, 1.1.1.1]
       parameters:
          stp: true
          forward-delay: 4
       dhcp4: no
       dhcp6: no

~~~
~~~
# sudo chmod 600 network.yaml
# sudo netplan generate
# sudo netplan apply
~~~
#### Setup hostname and domainname
~~~
# sudo hostnamectl set-hostname ubuntu.home.io
# sudo domainname home.io
# nslookup analytics.google.com 8.8.8.8
~~~
#### Set Local Time
~~~
# timedatectl
# sudo timedatectl set-timezone Europe/Brussels
# sudo dpkg-reconfigure tzdata
~~~
####  Install Samba Server
~~~
# sudo apt install samba
# mkdir -p /public/samba
# chmod 777 /public/samba
# sudo cp /etc/samba/smb.conf /root/smb-backup.conf
# sudo nano /etc/samba/smb.conf
~~~
~~~
[public]
  comment = public anonymous access 
  path = /public/samba/ 
  browsable = yes 
  create mask = 0660 
  directory mask = 0771 
  writable = yes 
  guest ok = yes
~~~
adding a user
~~~
sudo smbpasswd -a mohawkey
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY4NDY3MDE1NiwxNDA5MzU1NiwyODYzND
Y0MjksMjY2MjI2NzM3LC0xMjc0MzIzMDc0LDY0OTE0NTUwMiwx
Mjc0NzMxODU0XX0=
-->