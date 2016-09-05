---
layout: post
title:  "Загрузка файлов на amazon из c#"
date:   2016-09-05 18:14:02 +0300
categories: amazon s3 с#
---

# Загрузка файлов на amazon

## SDK

Доступно по [офф ссылке]


## Библиотеки

Через `nuget` скачиваем пакеты:

- Install-Package AWSSDK.Core -Version 3.1.11
- Install-Package AWSSDK.S3 -Pre

## Собственно сама загрузка

Требуется завести доп. пользователя на amazon и сформировать файл профиля вида, разрешением `.aws`:

```
[user-profile]
aws_access_key_id = KEY_HERE
aws_secret_access_key = ACCESS_KEY_HERE
```

Далее в config файле прописать:

```
<appSettings>
 <add key="AWSProfilesLocation" value="C:\\user.aws" />
 <add key="AWSProfileName" value="user-profile" />
 <add key="AWSRegion" value="ap-northeast-1" />
</appSettings>
```


```c#
var client = new AmazonS3Client();
var fileTransferUtility = new TransferUtility(client);

var file = new FileStream(path_to_file);
fileTransferUtility.Upload(image, "S3BucketName", name + ".jpg");
```


[офф ссылке]: https://aws.amazon.com/ru/sdk-for-net/