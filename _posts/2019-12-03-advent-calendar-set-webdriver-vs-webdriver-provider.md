---
layout: post
title: "setWebDriver или WebDriverProvider?"
description: ""
category:
header-text: "Selenide Advent Calendar<br/>День 3"
tags: []
---
{% include JB/setup %}

Добрый вечер!

На дворе 3 декабря, перед вами открыт рождественский календарь Selenide, и в сегодняшнем посте я отвечу на один простой вопрос.  

# Что выбрать, `setWebDriver()` или `WebDriverProvider`?

Как вы знаете, Selenide по умолчанию сам открывает и закрывает браузер. Вам для этого ничего не надо делать. 

Но иногда вам хочется открыть браузер с какими-то специфичными настройками. 
Для этого в селениде предусмотрены два разных метода. Так уж сложилось, что я никогда нигде не писал, почему понадобилось два, и как выбрать один из них.

Итак,

#### Вариант 1:

```java
  Configuration.browser = MyWebdriverProvider.class.getName();
``` 

Этот вариант предпочтительнее в большинстве случаев. Он хорош тем, что ваш класс `MyWebdriverProvider` отвечает только за то, КАК открыть браузер
(какие ключики ему передать, где найти бинарник, вот это всё). Но не отвечает за то, КОГДА открыть и закрыть браузер. 
Об этом заботится селенид.  

#### Вариант 2: 

```java
@Before
public void setUp() {
  var yandexBrowser = new YandexDriver(...);
  WebDriverRunner.setWebDriver(yandexBrowser);
}
``` 

Этот вариант хуже тем, что вам придётся самому не только открывать, но и закрывать браузер. Это часто вызывает недопонимание. 
Селенид не может его закрыть, ведь он не знает, где ещё вы можете его использовать. Сам открыл - сам закрывай.

Отсюда назревает вопрос:

### А зачем он вообще нужен?

Честно говоря, я сам долгое время не отдавал себе в этом отчёт, и лишь недавно назрело понимание. :)

Изначально метод `setWebDriver()` был добавлен для того, чтобы людям было легко перевести на селенид существующие 
проекты (не переписывая весь проект).

Представьте, что в компании X уже есть стотыщ автотестов на селениуме (или HtmlElements, Thucydides, Serenity или чём-то ещё).
И они хотят новые тесты писать на селениде, а старые не трогать. Работают и работают. 

И у них где-то в тестах уже есть код, которые открывает и закрывает браузер. И хотелось бы как-то сказать селениду, 
чтобы он тоже этот браузер использовал. Вот для этого и подходит метод `setWebDriver()`. Он позволяет запускать и 
старый, и новый код вперемежку, так чтобы они работали с одним и тем же браузером и не мешали друг дружке. 

То есть применение у метода `setWebDriver()`, как вы видите, довольно узкое. Я вообще не уверен, использовали ли его вообще
хоть когда-нибудь в таком ключе. 

## Что теперь?

А если вы пишите новый проект, используйте `WebDriverProvider`. SRP. 

<br/>


<br>

[Андрей Солнцев](http://asolntsev.github.io/)

ru.selenide.org