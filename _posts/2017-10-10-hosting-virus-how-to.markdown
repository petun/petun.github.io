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
```

После очистки можно запаковать все php файлы вот таким образом:
```
rm -rf core/cache/*
find . -name "*.php" -o -name "*.htaccess" | tar zcvf sitename.php-files.tgz -T -
```

Далее, если опять вирусня.. копируем архив из php в корень сайта, ну и далее:

```
find . -name "*.php" -o -name "*.htaccess" | xargs rm -rf
tar zxvf petun.ru.php.tgz
```

## Дополнительно

Можно так же поискать через:

`find location -ctime time_period`

Examples of time_period:

- More than 30 days ago: -ctime +30
- Less than 30 days ago: -ctime -30
- Exactly 30 days ago: -ctime 30

