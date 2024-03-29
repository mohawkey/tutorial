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
#### disable cloud-init
~~~
# sudo touch /etc/cloud/cloud-init.disabled
~~~
###  Install Samba Server
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
### Install UFW
~~~
# sudo apt install ufw
# ls /etc/ufw/applications.d/
# more /etc/ufw/applications.d/openssh-server
# sudo ufw allow Samba
# sudo ufw allow OpenSSH
# sudo ufw enable
# sudo ufw status verbose
~~~
### Install LXD container
~~~
# sudo snap install lxd
# sudo snap list

# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
…
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0     1G  0 part /boot/efi
├─sda2   8:2    0    25G  0 part /
├─sda3   8:3    0   200G  0 part 
└─sda4   8:4    0 705.5G  0 part /public 

# lxd init
Would you like to use LXD clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: yes
Name of the new storage pool [default=default]: default
Name of the storage backend to use (btrfs, dir, lvm, zfs, ceph) [default=zfs]: zfs
Create a new ZFS pool? (yes/no) [default=yes]: yes
Would you like to use an existing empty block device (yes/no) [default=no]: yes
Path to the existing block device: /dev/sda3
Would you like to connect to a MAAS server? (yes/no) [default=no]: no
Would you like to create a new local network bridge? (yes/no) [default=yes]: no
Would you like to configure LXD to use an existing bridge or host interface? [default=no]: yes
Name of the existing bridge or host interface: br0
Would you like the LXD server to be available over the network? (yes/no) [default=no]: no
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]: yes
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: no

ON ERROR : zpool create -m none -O compression=on default /dev/sda3 -f
ON ERROR : zpool destroy default

dev/sda3
# lxc storage list
+---------+--------+---------+-------------+---------+---------+ 
|  NAME   | DRIVER | SOURCE  | DESCRIPTION | USED BY |  STATE  | 
+---------+--------+---------+-------------+---------+---------+ 
| default | zfs    | default |             | 1       | CREATED | 
+---------+--------+---------+-------------+---------+---------+
# lxc storage show default
# lxc storage info default

# sudo zfs list
# zpool list

~~~
### Install Container
~~~
$ lxc launch ubuntu:22.04 docker-151 -n br0
~~~

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyMjM0NzY5OTksLTEzNjE2NDY3NjIsMz
Q0NTEyNzI5LC0xMjg2OTE4NDMzLDE5OTYzNjYzODUsLTE0MzQ1
MjY5ODAsLTY4NDY3MDE1NiwxNDA5MzU1NiwyODYzNDY0MjksMj
Y2MjI2NzM3LC0xMjc0MzIzMDc0LDY0OTE0NTUwMiwxMjc0NzMx
ODU0XX0=
-->