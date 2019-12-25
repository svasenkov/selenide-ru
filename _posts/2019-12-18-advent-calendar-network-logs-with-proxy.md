---
layout: post
title: "Как получить сетевые запросы с помощью прокси"
description: ""
category:
header-text: "Selenide Advent Calendar<br/>День 18"
tags: []
---
{% include JB/setup %}

Привет!

В предыдущих постах нашего рождественского календаря мы рассмотрели два способа получить сетевые запросы между браузером и приложением.  
Оба нас расстроили тем, что не позвояеют прочитать тело запроса/ответа. 

Наконец, дошла очередь до третьего способа - через встроенный прокси-сервер.

### Перед тестом

Как вы знаете, в селениде уже есть встроенный прокси-сервер, надо его всего лишь включить: 

```java
Configuration.proxyEnabled = true;
```

И ещё нужно сказать прокси-серверу, чтобы он начал отслеживать запросы:

```java
  BrowserMobProxy bmp = WebDriverRunner.getSelenideProxy().getProxy();
    
  // запоминать тело запросов (по умолчанию тело не запоминается, ибо может быть большим)
  bmp.setHarCaptureTypes(CaptureType.getAllContentCaptureTypes());

  // запоминать как запросы, так и ответы
  bmp.enableHarCaptureTypes(CaptureType.REQUEST_CONTENT, CaptureType.RESPONSE_CONTENT);

  // начинай запись!
  bmp.newHar("pofig");
```

### После теста

Теперь нужно получить HAR и анализировать все записи в нём: 

```java
    List<HarEntry> requests = bmp.getHar().getLog().getEntries();
``` 

HAR (HTTP Archive) - это типа такой "архив" со всеми сетевыми запросами и ответами ("entries").

Каждая запись - это и есть запрос от тестируемого приложения к серверу.  
Внутри есть все данные: URL, request, response, их http status и body.  
Всё, о чём мы так давно мечтали. 

<img src="{{BASE_PATH}}/images/2019/12/har.entries.png" alt="HAR entries"/>

### Плюсы:

* Есть все данные, которые нам нужны
* Легко анализировать программно 
* Работает во всех браузерах

### Мунусы:

Минус только один: иногда у людей возникают сложности с запуском прокси, когда браузер и тесты бегут на разных 
машинах, и с "браузерной" машины нет доступа к "тестовой" машине. 
Хотя я никогда не понимал, зачем такие сложности. Запускайте тесты и браузеры на одной и той же машине - ВСЁ будет в стотыщ раз ПРОЩЕ.  
Надо параллелить - параллельте тесты. 
Нужен кластер - запускайте ТЕСТЫ на разных нодах кластера (а с ними запустятся и браузеры). Зачем всё усложнять?


## Что теперь?

Теперь мы умеем читать сетевые запросы при прогоне тестов.  
Но я вообще-то надеюсь, что обычно это вам не должно быть нужно. Ну может, в каких-то очень уж запутанных случаях.  
Обычно должно быть достаточно почитать логи приложения, чтобы понять, какие запросы к нему прилетали.   Будьте проще. 

<br> 

[Андрей Солнцев](http://asolntsev.github.io/)

ru.selenide.org