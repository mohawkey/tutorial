# Ubuntu Server 22.04.4 LTS
## Main Server Installation
### Server Installation
Update & Package Installation
~~~
# sudo apt-get update
# sudo apt-get upgrade
# sudo apt-get install -y nano net-tools zfsutils bind9-utils
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
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMDExNjE2Miw2NDkxNDU1MDIsMTI3ND
czMTg1NF19
-->