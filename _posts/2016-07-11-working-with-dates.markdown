---
layout: post
title:  "Working with dates with php"
date:   2016-07-11 13:20:02 +0300
categories: php dates
---

Ссылка на оффициальную документацию: [http://php.net/manual/ru/class.dateinterval.php]

Формат начинается с буквы **P**, за "период". 

Длина каждого периода описывается целым значением и следующим за ним указателем периода.

Если длина периода содержит **время**, то его описанию должна предшествовать буква **T**

- P2D - 2 дня
- PT1H1M - 1 час одна минута
- P2DT2H23M - 2 дня, 2 часа 23 минуты


```php
$dateInterval = new DateInterval('P2DT2H23M);
```

[http://php.net/manual/ru/class.dateinterval.php]: http://php.net/manual/ru/class.dateinterval.php