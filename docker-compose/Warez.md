# Warez Stack
~~~
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
      - transmission:/config
      - /transmission/downloads:/downloads
      # - /transmission/watch:/watch
    ports:
      - 9091:9091
      - 51413:51413
      - 51413:51413/udp
    restart: unless-stopped
    
  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Brussels
    volumes:
      - powlarr:/config
    ports:
      - 9696:9696
    restart: unless-stopped
    
  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Brussels
    volumes:
      - radarr:/config
      - /transmission/downloads:/downloads
      # - /path/to/movies:/movies #optional
      # - /path/to/download-client-downloads:/downloads #optional
    ports:
      - 7878:7878
    restart: unless-stopped
    
volumes:
  transmission:
  powlarr:
  radarr:

networks: 
  nginx-proxy-net: 
    name: nginx-proxy-net 
    external: true
~~~
## Config Radarr
~~~
Settings -> Download Client -> Transmission

Remote Port mapping
192.168.1.151		/downloads/complete		/downloads.complete
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4NTk2Mjc0MzEsMTQwMjYyMjM2Nl19
-->