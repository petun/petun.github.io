---
layout: post
title:  "Docker шпаргалки"
date:   2016-09-13 23:14:02 +0300
categories: docker mysql
---


```bash
docker run --name app-db -p 3306:3306 -v /c/Users/User/docker/data_dir/:/var/lib/mysql -v /c/Users/User/docker/app-mysql.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=dev -e MYSQL_USER=user -d mysql:latest --explicit_defaults_for_timestamp

docker run --name dooglys-db -p 3307:3306 -v /var/docker_data/dooglys_db:/var/lib/mysql -v /var/docker_data/dooglys_mysql.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=dooglys -e MYSQL_DATABASE=dooglys -e MYSQL_USER=dooglys -e MYSQL_PASSWORD=dooglys -d mysql:latest --explicit_defaults_for_timestamp

docker exec -it dooglys-db bash
```