---
layout: post
title:  "Лечим хостинг от вирусов"
date:   2017-10-10 13:14:02 +0300
categories: htaccess apache
---

# Правильный путь очистки от вирусов

Найти все файлы, модифицированные позже 2017-10-01

```bash
find . -type f -newermt 2017-10-01 -name "*.php"

# delete all files which finds in prev command
find . -type f -newermt 2017-10-01 -name "*.php" | xargs rm -rf

# find another files
find . -type f -newermt 2017-10-01
```

Обязательно нужно провероить корневой .htaccess, если что, тупо заменить его на файл по умолчанию:
`cp ~/modx/modx-2.3.3-pl/ht.access .htaccess`

После очистки можно запаковать все php файлы вот таким образом:
```
rm -rf core/cache/*
find . -name "*.php" -o -name "*.htaccess" -o -name "*.js" | tar zcvf sitename.php-js-files.tgz -T -
```

Далее, если опять вирусня.. копируем архив из php в корень сайта, ну и далее:

```
find . -name "*.php" -o -name "*.htaccess" -o -name ".js" | xargs rm -rf
tar zxvf sitename.php-js-files.tgz 
```


Если ничего не помогает, можно просто поискать все файлы, содержащие закодированное в base64 содержимое, например, так:

`# find ./ -name '*.php' | xargs grep -E '[0-9a-zA-Z/]{80}'`

Эта команда найдет все скрипты PHP, в которых есть строки, похожие на base64 длиной не менее 80 символов.


## Анализ логов

Очень помогает в поиске источника вирусов анализ лог файлов веб сервера. Оттуда сразу можно понять, на какие скрипты идут запросы. Анализ лог файлов можно производить с помощью утилиты [goaccess](https://goaccess.io/)

## После лечения

После лечения ваш сайт попадает в список долбежки. На зараженные адреса постоянно идет большой трафик запросов. Если он доходит до php - то естественно сайт проседает. Удобно мониторить это в панели хостинга.. или по логам.. 
как вариант - можно использовать вот такой код для .htaccess

```
# special antivirus url routes
RewriteCond %{THE_REQUEST} ^.*engine/\d+\.html.+$ [NC]
RewriteRule ^(.*)$ - [F,L]
```

Может такое случиться, что вы вроже как везде все обновили, 100 раз перепроверили. И тут опа, и через недельку две у вас снова целый зоопарк. Что бы такого не случалось, первое что нужно сделать, это включить директиву `open_base_dir` для php. В этом случае заражение одного сайта никаким образом не скажется на работе других сайтов. 

Кроме этого, вот небольшой проверочный скрипт, который вам даст понимание того, на какие урлы у вас возвращается 200-ый код возврата. Это полезно что бы понимать, не заразился ли у вас еще какой то сайт.

```bash
# удаляем все логи, что бы проверку осуществлять здесь и сейчсс
rm *.log

# ждем минут 10, пока журналы не соберуться заново
grep "\" 200" *.log > 200.log
```

## Права на папки

Как вариант, можно запретить писать везде. Вот команда:
```bash
find . -type f -exec chmod 0444 {} \;
find . -type d -exec chmod 0555 {} \;
# chmod for all need dirs
chmod -R 777 core/cache
chmod 666 core/config/config.inc.php
chmod -R 777 core/export
chmod -R 777 core/packages
chmod -R 755 assets/
```

И наоборот, вернуть все на запись (например для обновления CMS):
```bash
find . -type f -exec chmod 0644 {} \;
find . -type d -exec chmod 0755 {} \;
```




## Дополнительно

Можно так же поискать через:

`find location -ctime time_period`

Examples of time_period:

- More than 30 days ago: -ctime +30
- Less than 30 days ago: -ctime -30
- Exactly 30 days ago: -ctime 30

Еще вирусня прячется в картинках, да да.. и такое бывает )
```bash
grep -l -R  "<?php" images/*
grep -l -R  "<?php" | grep ".gif$" | xargs rm
```

