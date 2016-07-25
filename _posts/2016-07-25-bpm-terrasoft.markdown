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

Если всего этого у вас нет, всегда можно просто взять [бесплатное демо на 14 дней] (при регистрации
 можно выбрать пакет **team**) и попробовать.
Но в этом случае у вас не будет возможности полноценно отлаживать серверный код.

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



# Примеры работы с серверным кодом (написание WCF сервисов)

## Получение данных

```cs
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
```

**Важно!** - что бы получить значение колонки из связаной таблицы, нужно обратиться к ней
вот таким образом: `ИмяСушности_ИмяКолонки`. Например:

```cs
item.GetColumnValue("RegionId").ToString();
item.GetColumnValue("Region_Name").ToString();
```

## Изменение сущностей

```cs
// Выполнение запроса к базе данных и получение объекта с заданным идентификатором.
var entity = esqResult.GetEntity(userConnection, new Guid(id));

entity.SetColumnValue("Link", link);
entity.SetBytesValue("Data", new byte[] { });
return entity.Save();
```


## Пример рабочего сервиса

```cs
using System.ServiceModel;
using System.ServiceModel.Web;
using System.ServiceModel.Activation;
using System.Configuration;
using System;
using System.IO;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data;
using Terrasoft.Common;
using Terrasoft.Core;
using Terrasoft.Core.DB;
using Terrasoft.Core.Entities;
using System.Web;
using Websharks.Loader;

namespace Terrasoft.Configuration.CustomConfigurationService
{

    // Класс сервиса помечен обязательными атрибутами [ServiceContract] и
    // [AspNetCompatibilityRequirements] с параметрами.
    [ServiceContract]
    [AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Required)]
    public class Loader
    {

        // Метод сервиса помечен обязательными атрибутами [OperationContract] и
        // [WebInvoke] с параметрами.
        [OperationContract]
        [WebInvoke(Method = "POST", RequestFormat = WebMessageFormat.Json, BodyStyle = WebMessageBodyStyle.Wrapped,
        ResponseFormat = WebMessageFormat.Json)]
        public LoadResult LoadPhoto(string photoId)
        {
            var errorMessage = "";
            try
            {
                var appSettings = ConfigurationSettings.AppSettings;

                var loader = new Websharks.Loader.Loader(
                    appSettings.Get("AWSS3BucketName"),
                    appSettings.Get("CloudFrontDomainName")
                    );

                var result = loader.LoadPhoto(GetFileFromDb(photoId), photoId, appSettings.Get("PhotoLoaderwaterMarkPath"));
                if (result != null)
                {
                    var link = result.ImageUrl;
                    if (SetFileLink(photoId, link))
                    {
                        return result;
                    }
                }

            }
            catch (Exception s3Exception)
            {
                errorMessage = s3Exception.Message;
            }

            // fail result
            return new LoadResult()
            {
                Error = true,
                ErrorMessage = errorMessage
            };
        }

        private Stream GetFileFromDb(string id)
        {
            var userConnection = (UserConnection)HttpContext.Current.Session["UserConnection"];

            // Создание запроса к схеме City, добавление в запрос колонки Name.
            var esqResult = new EntitySchemaQuery(userConnection.EntitySchemaManager, "PropertyGalleryImage");
            esqResult.AddColumn("Data");

            // Выполнение запроса к базе данных и получение объекта с заданным идентификатором.
            var entity = esqResult.GetEntity(userConnection, new Guid(id));

            var value = entity.GetBytesValue("Data");

            return new MemoryStream(value);
        }


        private bool SetFileLink(string id, string link)
        {
            var userConnection = (UserConnection)HttpContext.Current.Session["UserConnection"];

            // Создание запроса к схеме City, добавление в запрос колонки Name.
            var esqResult = new EntitySchemaQuery(userConnection.EntitySchemaManager, "PropertyGalleryImage");
            esqResult.AddAllSchemaColumns();

            // Выполнение запроса к базе данных и получение объекта с заданным идентификатором.
            var entity = esqResult.GetEntity(userConnection, new Guid(id));

            entity.SetColumnValue("Link", link);
            entity.SetBytesValue("Data", new byte[] { });
            return entity.Save();
        }


        [OperationContract]
        [WebInvoke(Method = "POST", RequestFormat = WebMessageFormat.Json, BodyStyle = WebMessageBodyStyle.Wrapped,
      ResponseFormat = WebMessageFormat.Json)]
        public string RemovePhoto(string photoId)
        {
            try
            {
                var appSettings = ConfigurationSettings.AppSettings;

                var loader = new Websharks.Loader.Loader(
                    appSettings.Get("AWSS3BucketName"),
                    appSettings.Get("CloudFrontDomainName")
                    );

                return loader.RemovePhotos(new List<string> {
                    photoId + ".jpg", photoId + "_thumb.jpg"
                }.ToArray()).ToString();
            }
            catch (Exception s3Exception)
            {
                return s3Exception.Message;
            }
        }
    }
}
```

Доступ по данного сервиса после установки будет следующим: `http://exmaple.com/0/rest/ServiceName/MethodName`

**Важно!** Доступ до сервисов осуществляется ТОЛЬКО если пройдена процедура авторизации,
соответсвенно для того, что бы взаимодействовать со своими сервисами извне, требуется первым делом авторизоваться в системе
и далее уже осуществлять запросы, предоставляя авторизационные куки.
Вот небольшой пример, написаный с использованием yii2-browser.

```php
<?php
/**
 * Created by PhpStorm.
 * User: User
 * Date: 20.07.2016
 * Time: 15:31
 */

namespace common\components;


use yii\base\Component;
use yii\httpclient\Client;
use yii\web\Cookie;

class TerrasoftODataClient extends Component
{

    public $domain;

    public $userName;

    public $userPassword;

    protected $_entityServiceUri;

    protected $_authServiceUri;

    /**
     * @var Client
     */
    protected $_client;

    protected $_autoCookies;


    public function init() {
        $this->_entityServiceUri = sprintf('http://%s/0/ServiceModel/EntityDataService.svc/', $this->domain);;
        $this->_authServiceUri = sprintf('http://%s/ServiceModel/AuthService.svc/Login', $this->domain);
        $this->_client = new Client();
    }

    public function authorize() {
        $response = $this->_client->createRequest()
            ->setMethod('POST')
            ->setFormat(Client::FORMAT_JSON)
            ->setUrl($this->_authServiceUri)
            ->setHeaders([
                'ContentType' => 'application/json',
                'Accept' => 'application/json'
            ])->setData(
                [
                    'UserName' => $this->userName,
                    'UserPassword' => $this->userPassword,
                    'TimeZoneOffset' => '-180'
                ]
            )->send();

        if ($response->statusCode == 200 && $response->cookies->get('.ASPXAUTH') instanceof Cookie) {
            $this->_autoCookies = $response->cookies;
            return true;
        }

        return false;
    }

    public function getContacts()
    {
        $url = $this->_constructSelectUrl('ContactCollection', ['Id', 'Name']);
        $data = $this->_makeDataResponse($url);

        return $data;
    }

    private function _constructSelectUrl($collection, array $selectFields)
    {
        return $this->_entityServiceUri . $collection . '?select=' . implode(',', $selectFields);
    }

    private function _makeDataResponse($url) {
        if ($this->_autoCookies) {
            $response = $this->_client->createRequest()
                ->setMethod('GET')
                ->setUrl($url)
                ->setCookies($this->_autoCookies)
                ->setHeaders(['ContentType' => 'application/json'])
                ->send();

            if ($response->getIsOk()) {
                return $response->data;
            }
        }

        return false;
    }
}

```


[офф документацию]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/dokumentaciya-po-razrabotke-bpmonline
[почитать тут]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-klientskogo-koda
[http://www.community.terrasoft.ru/]: http://www.community.terrasoft.ru/
[https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-servernogo-koda]: https://academy.terrasoft.ru/documents/technic-sdk/7-7-0/otladka-servernogo-koda
[бесплатное демо на 14 дней]: https://www.terrasoft.ru/trial?product=ru-sales-omnichannel