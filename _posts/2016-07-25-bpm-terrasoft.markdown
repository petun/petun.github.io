---
layout: post
title:  "Заметки по разработке на платформе BPM Online"
date:   2016-07-25 13:14:02 +0300
categories: terrasoft bpm-online
---


# Основные ссылки и ресурсы
Во первых, нужно обязательно зарегистрироваться на форуме [http://www.community.terrasoft.ru/]
Именно тут можно найти ответы на практически любые вопросы по системе.

Ну и конечно нужно изучить [офф документацию] для разработчиков.


# Окружение
Что бы начать разрабатывать для платформы, необходимо иметь следующие компоненты в системе:

  1.  Windows 2012 R2
  2.  MSSQL Server 2008
  3.  Visual Studio 2015 - тут подойдет даже Community Edition
  4.  SQL Server Management Studio
  5.  Ну и конечно сама система BPM Online + инструкции по установке (в моем случае это был небольшой pdf файл)


# Отладка кода

## Серверный код (c#)

Одним из ключевых моментов, который влияет на скорость и качество разработки конечно является
 возможность серверной отладки кода.
По большому счету не буду переписывать оригинал, даю просто ссылку, где достаточно подробно расписано как все настроить.
[https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-servernogo-koda]

## Клиентский код

К сожалению так и не удалось узнать, каким образом можно разрабатывать клиентский код, не вставляя
его в пакеты, а напрямую из ФС, но по моему это можно каким то образом сделать.
В результате, приходится добавлять `debugger` в код, что бы автоматом вызывалась отладка.
Подробнее можно [почитать тут].



# Примеры работы с серверным EntityQuery
## Получение данных

    var userConnection = (UserConnection)HttpContext.Current.Session["UserConnection"];

    // Создание экземпляра запроса EntitySchemaQuery с корневой схемой "City".
    var esqQuery = new EntitySchemaQuery(userConnection.EntitySchemaManager, "Property");

    // Добавление в запрос колонки с наименованием страны, которой принадлежит город.
    esqQuery.AddAllSchemaColumns();

    esqQuery.AddColumn("PropertyType.Name");
    esqQuery.AddColumn("City.Name");

    // Выполнение запроса к базе данных и получение объекта с заданным идентификатором.
    var collection = esqQuery.GetEntityCollection(userConnection);
    var entity = esqQuery.GetEntity(userConnection, new Guid(id));

**Важно!** - что бы получить значение колонки из связаной таблицы, нужно обратиться к ней
вот таким образом: `ИмяСушности_ИмяКолонки`. Например:

    item.GetColumnValue("RegionId").ToString();
    item.GetColumnValue("Region_Name").ToString();


[офф документацию]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/dokumentaciya-po-razrabotke-bpmonline
[почитать тут]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-klientskogo-koda
[http://www.community.terrasoft.ru/]: http://www.community.terrasoft.ru/
[https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-servernogo-koda]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-servernogo-koda