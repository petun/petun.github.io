---
layout: post
title:  ".htaccess useful configs"
date:   2016-07-11 13:14:02 +0300
categories: htaccess apache
---------------------------

``` 
Order allow,deny
Deny from all
```

```
# replace jpg 
RewriteRule ^netcat_files/.+\.jpg$ /1.jpg [R=301,L]
```


```
# redirect all site
RewriteCond %{HTTP_HOST} ^vyksa-rf.ru [NC]
RewriteRule ^(.*)$ http://xn--80ady2a0c.xn--p1ai/$1 [R=301,L]
```