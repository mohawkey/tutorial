# install

~~~
$ lsb_release -a

No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy


$ lxc remote list

$ lxc image list ubuntu: | grep LTS | grep CONTAINER | grep 22 | grep amd64 | grep 2024


# lxc launch ubuntu:22.04 docker-152 -n br0
# lxc exec docker-152 bash
# apt install openvswitch-switch ( misschien niet nodig )
# nano /etc/netplan/50-cloud-init.yaml
~~~

install apt-file
apt-file update 
$ apt-file search ovsdb-server.service
openvswitch-switch: /lib/systemd/system/ovsdb-server.service

# dd
~~~
nano eth0.yaml
~~~
~~~
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: false
      addresses: [192.168.1.152/24]
      routes:
         - to: default
           via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
~~~
~~~
$ sudo 
~~~

