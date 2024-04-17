# Transmission
## configuration
~~~
# mkdir /public/container/transmission/downloads/complete -p
# mkdir /public/container/transmission/watch -p

# chmod 777 /public/container/transmission/downloads -R
# chmod 777 /public/container/transmission/watch

# chown nobody:nogroup /public/container/transmission/downloads -R
# chown nobody:nogroup /public/container/transmission/watch

# lxc config device add docker-151 transmission disk \
	source=/public/container/transmission \
	path=/transmission
~~~
~~~
$ lxc config device show docker-151
~~~
~~~
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
#      - TRANSMISSION_WEB_HOME= #optional
#      - USER= #optional
#      - PASS= #optional
#      - WHITELIST= #optional
#      - PEERPORT= #optional
#      - HOST_WHITELIST= #optional
    volumes:
      - data:/config
      - /transmission/downloads:/downloads
      - /transmission/watch:/watch
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
eyJoaXN0b3J5IjpbLTIxMDcxMTQ4MjQsODUyNTYwMTg2LC0xND
Q4NjkzOTMwLDExNTEyMTE1OTRdfQ==
-->