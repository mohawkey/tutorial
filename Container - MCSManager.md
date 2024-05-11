### Install Container
~~~
$ lxc launch ubuntu:22.04 mscmanager-155 -n br0
~~~
#### ip
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
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NTE5ODg4OTldfQ==
-->