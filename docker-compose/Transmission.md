# Transmission
## configuration
~~~
mkdir /public/container/transmission/downloads
lxc config device add warez usb-drive disk source=/media/usb-drive path=/storage/usb-drive
$ lxc config device show warez

$ sudo lxc config device remove warez usb-drive

~~~
## docker-compose.yaml
~~~yaml
---
services:
  transmission:
    image: lscr.io/linuxserver/transmission:latest
    container_name: transmission
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe\Brussels
      - TRANSMISSION_WEB_HOME= #optional
      - USER= #optional
      - PASS= #optional
      - WHITELIST= #optional
      - PEERPORT= #optional
      - HOST_WHITELIST= #optional
    volumes:
      - data:/config
      - /path/to/downloads:/downloads
      - /path/to/watch/folder:/watch
    ports:
      - 9091:9091
      - 51413:51413
      - 51413:51413/udp
    restart: unless-stopped
    
volumes:
	data:
	
networks: 
	nginx-proxy-net: 
	name: nginx-proxy-net 
	external: true
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjA4MjQwNjQ5LDExNTEyMTE1OTRdfQ==
-->