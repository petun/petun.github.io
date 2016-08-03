---
layout: post
title:  "Требования к верстке HTML с использованием twitter bootstrap"
date:   2016-08-03 13:14:02 +0300
categories: bootstrap yii2
---


# Общие требования к CSS

  1. Должны использоваться препроцессоры SASS, LESS или STYLUS.
  2. Верстка по БЭМ. В данном случае не так важен сам факт использования методологии,
сколько правильное разделение всей структуры на блоки.
  3. Важно! Каждый блок должен быть описан в своем собственном файле, требуется следующая
структура:

```
project
└───css
    │   style.scss
    │   _layout.scss
    │   _base.scss
    │   _fonts.scss
    │
    ├───blocks
    │   │   _news.scss
    │   │   _gallery.scss
    │   │   _product.scss
    │   │   _another_one_block.scss
```

# Формы

Все формы должны быть представлены с помощью [стандартных классов], поставляющихся с bootstrap.
Если требуется сделать не совсем стандартную форму, требуется добавлять свои собственные
классы к корневому элементу `form`, не меняя внутренней разметки.

Пример:

```html
<form class="no-standart-form">
    <div class="form-group">
        <label for="exampleInputEmail1">Email address</label>
        <input type="email" class="form-control" id="exampleInputEmail1" placeholder="Email">
    </div>
    <div class="form-group">
        <label for="exampleInputPassword1">Password</label>
        <input type="password" class="form-control" id="exampleInputPassword1" placeholder="Password">
    </div>
    <button type="submit" class="btn btn-default">Submit</button>
</form>
```

# Кнопки
У bootstrap есть свой [набор классов], описывающий кнопки.

Для добавления своих модификаций **важно**, что бы цвет и размеры описывались разными классами.
Например:

```css
.btn.btn-blue {
    background: blue;
    color: white;
}


.btn.btn-extra-large {
    font-size: 40px;
    padding: 40px 50px;
}
```


# Адаптивная верстка
Адаптивная верстка должна осуществляться ТОЛЬКО с помощью стандартной [сетки bootstrap].

**Очень важно!** Адаптивность для блоков **должна начинаться** с мобильной версии.
Медиа селекторы следует помещать в тот же файл, где описывается основной стиль для блока
Т.е. специального файла (например mobile.css) создавать не следует.

Например:

```css
/* мобильный вариант */
.news-item {
    width: 100%;
}


@media (min-width: 768px) {
    .news-item {
        width: 80%;
    }
}

@media (min-width: 992px) {
    .news-item {
        width: 20%;
    }
}
```

# Yii2 + Bootstrap
Yii2 Framework предоставляет очень большие возможности в плане интеграции со
стандартныи компонентами bootstrap. Поэтому, при верстке сайтов под yii2 необходимо
обязательно использовать стандартную разметку и стили, предоставляемые bootstrap.

Список компонентов следующий:

- Form
- Button
- Modal (модальное окно)
- Tab
- Navigation Bar (основное меню или дополнительные меню)
- Alert
- Carousel (слайдер)
- Progress



[сетки bootstrap]: http://getbootstrap.com/css/#grid
[набор классов]: http://getbootstrap.com/css/#buttons
[стандартных классов]: http://getbootstrap.com/css/#forms