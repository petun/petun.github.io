---
layout: post
title:  "Лечим хостинг от вирусов"
date:   2017-10-10 13:14:02 +0300
categories: htaccess apache
---

Найти все файлы, модифицированные позже 2017-10-01

```bash
find . -type f -newermt 2017-10-01 -name "*.php"

# delete all files which finds in prev command
find . -type f -newermt 2017-10-01 -name "*.php" | xargs rm -rf
```

Можно так же поискать через:

`find location -ctime time_period`

Examples of time_period:

- More than 30 days ago: -ctime +30
- Less than 30 days ago: -ctime -30
- Exactly 30 days ago: -ctime 30

