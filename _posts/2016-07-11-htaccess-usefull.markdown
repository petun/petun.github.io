---
layout: post
title:  "Шпаргалки для apache htaccess"
date:   2016-07-11 13:14:02 +0300
categories: htaccess apache
---

Полезные настройки для php

```
AddDefaultCharset utf-8

php_value error_reporting 7
php_flag  display_errors on
php_flag  log_errors on
php_value error_log  error.log

php_value session.use_only_cookies 1
php_value session.use_trans_sid 0

php_value upload_max_filesize 512M
php_value post_max_size 512M

php_value memory_limit 64M
```

Запрет доступа на папку

``` 
Order allow,deny
Deny from all
```

Заменить все недостающие картинки на одну:

```
# replace jpg 
RewriteRule ^netcat_files/.+\.jpg$ /1.jpg [R=301,L]
```



```
# redirect all site
RewriteCond %{HTTP_HOST} ^vyksa-rf.ru [NC]
RewriteRule ^(.*)$ http://xn--80ady2a0c.xn--p1ai/$1 [R=301,L]
```