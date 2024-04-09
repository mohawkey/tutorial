# PostgreSQL
## Connecting to the database
~~~
$ psql --host=portainer.home.io --port=5432 --username=mohawkey --dbname=mohawkey
~~~
## Installation
~~~
services:
  db:
    image: postgres:latest
    container_name: postgres
    restart: always
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: ${PGSQL_USER}
      POSTGRES_PASSWORD: ${PGSQL_PASS}
    volumes:
      - postgres-data:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: pgadmin
    restart: always
    ports:
      - "5050:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_USER}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_PASS}
    volumes:
      - pgadmin-data:/var/lib/pgadmin

volumes:
  postgres-data:
  pgadmin-data:
~~~
or
~~~
version: "3.9"

services:
  postgres:
    image: postgres:16.2-alpine
    container_name: postgres
    restart: always
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASSWORD=${PGSQL_PASSWORD}
      - POSTGRES_USER=${PGSQL_USER}
      - POSTGRES_DB=${PGSQL_DB}
    volumes:
      - postgres:/var/lib/postgresql/data
      
volumes:
  postgres
~~~
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0MDk4MjA3MF19
-->