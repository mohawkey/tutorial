~~~
services:
  crafty:
    image: registry.gitlab.com/crafty-controller/crafty-4:latest
    container_name: crafty
    restart: always
    ports:
        - "8000:8000" # HTTP
        - "8443:8443" # HTTPS
        - "25500:25500"
        # - "8123:8123" # DYNMAP
        # - "19132:19132/udp" # BEDROCK
        # - "25500-25600:25500-25600" # MC SERV PORT RANGE
    environment:
        - TZ=Europe/Brussels
    volumes:
        - crafty-backups:/crafty/backups
        - crafty-logs:/crafty/logs
        - crafty-servers:/crafty/servers
        - crafty-config:/crafty/app/config
        - crafty-import:/crafty/import

volumes:
  crafty-backups:
  crafty-logs:
  crafty-servers:
  crafty-config:
  crafty-import:

networks: 
  nginx-proxy-net: 
    name: nginx-proxy-net 
    external: true
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc0Njg1MDY2NywtMTI2NDk2MDg3LDE5Mz
Y4NDEzNjBdfQ==
-->