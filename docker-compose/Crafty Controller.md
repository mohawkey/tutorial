~~~
services:
  crafty:
	image: registry.gitlab.com/crafty-controller/crafty-4:latest
    container_name: crafty_container    
    restart: always
    ports:
        - "8000:8000" # HTTP
        - "8443:8443" # HTTPS
        - "25500
        # - "8123:8123" # DYNMAP
        # - "19132:19132/udp" # BEDROCK
        # - "25500-25600:25500-25600" # MC SERV PORT RANGE
    environment:
        - TZ=Europe/Brussels
    volumes:
        - ./docker/backups:/crafty/backups
        - ./docker/logs:/crafty/logs
        - ./docker/servers:/crafty/servers
        - ./docker/config:/crafty/app/config
        - ./docker/import:/crafty/import
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM0OTg5MDY1MCwxOTM2ODQxMzYwXX0=
-->