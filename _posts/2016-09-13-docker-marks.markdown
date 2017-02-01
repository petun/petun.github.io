---
layout: post
title:  "Docker how-to"
date:   2016-09-13 23:14:02 +0300
categories: docker mysql
---

## Commands

- `docker images` - show all available images
- `docker network ls` - show networks
- `docker network inspect bridge` - show network info
- `docker inspect web` - show machine information
- `docker-compose down && docker-compose up` - rebuild volumes



```bash
docker run --name app-db -p 3306:3306 -v /c/Users/User/docker/data_dir/:/var/lib/mysql -v /c/Users/User/docker/app-mysql.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=dev -e MYSQL_USER=user -d mysql:latest --explicit_defaults_for_timestamp

docker run --name dooglys-db -p 3307:3306 -v /var/docker_data/dooglys_db:/var/lib/mysql -v /var/docker_data/dooglys_mysql.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=dooglys -e MYSQL_DATABASE=dooglys -e MYSQL_USER=dooglys -e MYSQL_PASSWORD=dooglys -d mysql:latest --explicit_defaults_for_timestamp

docker exec -it dooglys-db bash
```


## I want to connect from a container to a service on the host
The Mac has a changing IP address (or none if you have no network access).
Our current recommendation is to attach an unused IP to the lo0 interface on the Mac;
for example: `sudo ifconfig lo0 alias 10.200.10.1/24`, and make sure that your service is listening on this address
or 0.0.0.0 (ie not 127.0.0.1). Then containers can connect to this address.


## Logs
`syslog -k Sender Docker`

## Simple lamp installation
```yaml
version: '2'

volumes:
  mysql_db:
  
  web:
   image: janes/alpine-lamp      
   restart: always
   ports:
    - "8888:80"
   volumes:
    - ./src:/www/

## Sample modx docker-compose
```yaml
version: '2'

volumes:
  mysql_db:


services:
    db:
     image: mysql:5.7
     volumes:
       - mysql_db:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: modx
       MYSQL_DATABASE: modx
       MYSQL_USER: modx
       MYSQL_PASSWORD: modx


    web:
      build: .
      depends_on:
        - db
      restart: always
      ports:
        - "8111:80"
      volumes:
        - ./src:/app/
```
