# Ubuntu Server 22.04.4 LTS
## Ubuntu Host Installation
### Server Installation
Server Installation - Partition Schema
~~~
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0     1G  0 part /boot/efi				[EFI Boot Partition]
├─sda2   8:2    0    25G  0 part /						[Ubuntu 22.04]
├─sda3   8:3    0   200G  0 part 						[ZFS Partition LXD Container]
└─sda4   8:4    0 705.5G  0 part /public				[Samba Folder]
~~~
~~~
mkdir /public/samba/public -p
mkdir /public/samba/media/movies -p
mkdir /public/samba/media/pictures -p
mkdir /public/samba/media/series -p
mkdir /public/container/qbittorrent/downloads -p
~~~
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
  path = /public/samba/public 
  browsable = yes 
  create mask = 0660 
  directory mask = 0771 
  writable = yes 
  guest ok = yes
  
[media]
  comment = public anonymous access 
  path = /public/samba/media 
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
### NFS Server
~~~
** on the server
sudo apt update
sudo apt install nfs-kernel-server

** on the client
sudo apt update
sudo apt install nfs-common

# sudo mkdir /public/nfs/qbittorrent/downloads -p
# sudo chown nobody:nogroup /public/nfs/qbittorrent/downloads

# ls -ld /public/nfs/qbittorrent/downloads 
drwxr-xr-x 2 nobody nogroup 4096 Apr  8 10:45 /public/nfs/qbittorrent/downloads
~~~
~~~
sudo nano /etc/exports
/public/nfs/qbittorrent/downloads  192.168.1.151(rw,sync,no_subtree_check)

systemctl restart nfs-kernel-server
ufw allow from 192.168.1.151 to any port nfs
( ufw allow nfs )
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

$ lxc config set docker-151 security.nesting=true 
$ lxc config set docker-151 security.syscalls.intercept.setxattr=true
$ lxc config set docker-151 security.syscalls.intercept.mknod=true
$ lxc restart docker-151
~~~
#### ip
~~~
# sudo nano /etc/netplan/network.yaml
~~~
~~~
~~~ 
#### Install docker
~~~
$ lxc exec docker-151 bash

apt-get update
apt-get install ca-certificates curl
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
~~~
#### Portainer docker
~~~
$ docker volume create portainer
$ docker pull portainer/portainer-ce:latest
$ docker run -d -p 8000:8000 -p 9443:9443 \
    --name portainer --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer:/data portainer/portainer-ce:latest
~~~
#### adGuard docker
disable systemd-resolved service
~~~
# sudo systemctl disable systemd-resolved.service
# sudo systemctl stop systemd-resolved.service
~~~
~~~
version: "3"
services:
  adguard:
    image: adguard/adguardhome
    container_name: adguard
    ports:
      - 53:53/tcp
      - 53:53/udp
      # - 784:784/udp
      # - 853:853/tcp
      - 3000:3000/tcp
      - 1080:80/tcp
      - 1443:443/tcp
    volumes:
      - data:/opt/adguardhome/work
      - config:/opt/adguardhome/conf
    restart: unless-stopped
    networks:
      - nginx-proxy-net
    
volumes:
  data:
  config:

networks:
  nginx-proxy-net:
    name: nginx-proxy-net
    external: true
~~~
### nginx-proxy-manager docker
enable resolve
~~~
pull
~~~
~~~
version: '3.8'

services:
  nginx:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx
    restart: unless-stopped
    ports:
      - 80:80
      - 443:443
      - 81:81

    environment:
      DISABLE_IPV6: 'true'

    volumes:
      - data:/data
      - letsencrypt:/etc/letsencrypt

    networks:
      - nginx-proxy-net

volumes:
  data:
  letsencrypt:

networks:
  nginx-proxy-net:
    name: nginx-proxy-net
    external: true
~~~
### Qbittorrent
~~~
~~~
### Qbittorrent
~~~
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ5NjEwMjU0NiwxNDM0NzU2NDEzLC02Mz
Y5MjEwMDEsMjEyODE0OTk2NSwyNDY3NjI1NTEsLTY4OTUxNjkz
NCwxNjc1MzkxNDgzLC00MjEwODA0NTgsLTIxMTczMTc2MTIsLT
c3OTc2MTcwMywtMTg0MTAwNzg4MCwyMDk1NjY4NTA4LDY5NzAz
MDE4OSwtMTA1NzQ0MzkzMSwtMTA1MjY4MzY1OCwtMTA1OTc0OT
I0NSwtNzgwNzE5MjA5LC0xMjIzNDc2OTk5LC0xMzYxNjQ2NzYy
LDM0NDUxMjcyOV19
-->