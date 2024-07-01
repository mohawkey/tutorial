mount samba to docker

~~~
$ lxc config device show docker-151
eth0:
  name: eth0
  nictype: bridged
  parent: br0
  type: nic
qbittorrent:
  path: /qbittorrent
  source: /public/container/qbittorrent
  type: disk
transmission:
  path: /transmission
  source: /public/container/transmission
  type: disk
~~~
~~~
mkdir -p /public/container/sabnzbd
~~~
~~~
sudo lxc config device add docker-151 sabnzbd disk source=/public/container/sabnzbd path=/sabnzbd

~~~
