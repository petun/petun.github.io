---
layout: post
title:  "Docker шпаргалки"
date:   2016-09-13 23:14:02 +0300
categories: docker mysql
---


```bash
docker run --name app-db -p 3306:3306 -v /c/Users/User/docker/data_dir/:/var/lib/mysql -v /c/Users/User/docker/app-mysql.d:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=dev -e MYSQL_USER=user -e MYSQL_ROOT_PASSWORD=root -d mysql:latest --explicit_defaults_for_timestamp
```
