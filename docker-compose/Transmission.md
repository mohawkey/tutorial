# Transmission
## configuration
~~~
# mkdir /public/container/transmission/downloads -p
# mkdir /public/container/transmission/watch -p

# chmod 777 /public/container/transmission/downloads
# chmod 777 /public/container/transmission/watch

# chown nobody:nogroup /public/container/transmission/downloads
# chown nobody:nogroup /public/container/transmission/watch

$ lxc config device add docker-151 transmission disk \
	source=/public/container/transmission \
	path=/public

$ lxc config device show docker-151

$ lxc config device remove docker-151 transmission

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
eyJoaXN0b3J5IjpbLTE5MTg4MTE4OTUsMTE1MTIxMTU5NF19
-->