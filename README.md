# httpbin

[![Release](https://img.shields.io/github/release/Stivo182/oscript-httpbin.svg)](https://github.com/Stivo182/oscript-httpbin/releases)
[![Тестирование](https://github.com/stivo182/oscript-httpbin/actions/workflows/test.yml/badge.svg?branch=main)](https://github.com/stivo182/oscript-httpbin/actions/workflows/test.yml)
[![Статус порога качества](https://sonar.openbsl.ru/api/project_badges/measure?project=httpbin&metric=alert_status&token=sqb_2f7c84743fd1b295085c25a1b96cc8d975cd4dc7)](https://sonar.openbsl.ru/dashboard?id=httpbin)
[![Покрытие](https://sonar.openbsl.ru/api/project_badges/measure?project=httpbin&metric=coverage&token=sqb_2f7c84743fd1b295085c25a1b96cc8d975cd4dc7)](https://sonar.openbsl.ru/dashboard?id=httpbin)

Cервис позволяющий локально тестировать HTTP клиент. Разработан на [OneScript](https://github.com/EvilBeaver/OneScript) + [WINOW](https://github.com/autumn-library/winow). Поддерживает бо́льшую часть эндпоинтов [httpbin.org](https://httpbin.org/).

* 1\. [Установка](#installation)
* 2\. [Использование](#usage)
    * 2.1\. [CLI приложение](#cli-app)
    * 2.2\. [Тестирование через asserts и 1connector](#testing)
    * 2.3\. [Swagger UI](#swagger-ui)
* 3\. [Совместимость](#compatibility)
* 4\. [Программный интерфейс](#api)
* 5\. [Ограничения](#limitations)
* 6\. [Сравнение с httpbin.org](#comparison)

## Установка <a name="installation"></a>

``` bash
opm install httpbin
```

## Использование <a name="usage"></a>

### CLI приложение <a name="cli-app"></a>

Запуск сервиса через команду **run**: `httpbin run`

Опции команды:</br>
`-h`, `--host` - имя хоста / IP адрес сервиса</br>
`-p`, `--port` - порт сервиса

### Тестирование через [asserts](https://github.com/oscript-library/asserts) и [1connector](https://github.com/vbondarevsky/1connector) <a name="testing"></a>

_test.os:_
``` bsl
#Использовать asserts
#Использовать 1connector
#Использовать httpbin

Перем HttpBin;

&Инициализация
Процедура ПередЗапускомТестов() Экспорт
    HttpBin = Новый HttpBin();
    HttpBin.Запустить();
КонецПроцедуры

&Завершение
Процедура ПослеЗапускаТестов() Экспорт
    HttpBin.Остановить();
КонецПроцедуры

&Тест
Процедура ТестДолжен_ПроверитьПараметрыЗапроса() Экспорт
    ПараметрыЗапроса = Новый Структура();
    ПараметрыЗапроса.Вставить("key", "value");

    Ответ = КоннекторHTTP.Get(HttpBin.URL() + "/get", ПараметрыЗапроса);

    Ожидаем.Что(Ответ.КодСостояния).Равно(200);
    Ожидаем.Что(Ответ.Заголовки["Content-Type"]).Содержит("application/json");
    Ожидаем.Что(Ответ.Json()["args"]["key"]).Равно("value");
КонецПроцедуры
```

### Swagger UI

На стартовой странице сервиса (адрес по умолчанию: `http://127.0.0.1:3334`) доступна визуальная документация API, а также возможность отправки запросов и получения ответов.

## Совместимость <a name="compatibility"></a>

<table>
  <thead>
    <tr>
      <th colspan="2">Windows</th>
      <th colspan="2">Linux</th>
      <th colspan="2">MacOS</th>
    </tr>
    <tr>
      <th>OneScript 1.9</th>
      <th>OneScript 2.0</th>
      <th>OneScript 1.9</th>
      <th>OneScript 2.0</th>
      <th>OneScript 1.9</th>
      <th>OneScript 2.0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center">✅</td>
      <td align="center">✅</td>
      <td align="center">✅</td>
      <td align="center">✅</td>
      <td align="center">✅</td>
      <td align="center">✅</td>
    </tr>
  </tbody>
</table>

## Программный интерфейс <a name="api"></a>

### Класс `HttpBin`

Сервис по умолчанию запускается по адресу `127.0.0.1:3334` в фоновом режиме и с ожиданием завершения запуска сервиса.</br>
Класс реализован с текучим интерфейсом.

| Метод | Описание |
| --- | --- |
| `Запустить()` | Запускает сервис |
| `Остановить()` | Останавливает сервис |
| `URL()` | URL сервиса |
| `Хост()` | Хост сервиса |
| `Порт()` | Порт сервиса |
| `УстановитьХост(<Хост>)` | Устанавливает хост сервиса |
| `УстановитьПорт(<Порт>)` | Устанавливает порт сервиса |
| `ЗапускатьВФоне(<Флаг>)` | Запуск сервиса будет выполнен в фоновом режиме |
| `ОжидатьЗапуск(<Флаг>)` | Ожидать завершение запуска сервиса, запущенного в фоновом режиме |
| `УстановитьТаймаутОжидания(<Таймаут>)` | Устанавливает таймаут ожидания запуска сервиса, запущенного в фоновом режиме |

## Ограничения <a name="limitations"></a>

На данный момент нет поддержки https.

## Сравнение с httpbin.org <a name="comparison"></a>

| Эндпоинт | oscript-httpbin | httpbin.org |
| --- | :-: | :-: |
| `/ip` | ✅ | ✅ |
| `/uuid` | ✅ | ✅ |
| `/uuid/:n` | ✅ | ❌ |
| `/user-agent` | ✅ | ✅ |
| `/headers` | ✅ | ✅ |
| `/get` | ✅ | ✅ |
| `/post` | ✅ | ✅ |
| `/put` | ✅ | ✅ |
| `/patch` | ✅ | ✅ |
| `/delete` | ✅ | ✅ |
| `/anything` | ✅ | ✅ |
| `/anything/:anything` | ✅ | ✅ |
| `/base64/:value` | ✅ | ✅ |
| `/encoding/utf8` | ✅ | ✅ |
| `/gzip` | ✅ | ✅ |
| `/deflate` | ✅ | ✅ |
| `/brotli` | ✅ | ✅ |
| `/zstd` | ✅ | ❌ |
| `/status/:code` | ✅ | ✅ |
| `/response-headers?key=val` | ✅ | ✅ |
| `/redirect/:n` | ✅ | ✅ |
| `/redirect-to?url=foo` | ✅ | ✅ |
| `/redirect-to?url=foo&status_code=307` | ✅ | ✅ |
| `/relative-redirect/:n` | ✅ | ✅ |
| `/absolute-redirect/:n` | ✅ | ✅ |
| `/cookies` | ✅ | ✅ |
| `/cookies/set?name=value` | ✅ | ✅ |
| `/cookies/set/:name/:value` | ✅ | ✅ |
| `/cookies/delete?name` | ✅ | ✅ |
| `/basic-auth/:user/:passwd` | ✅ | ✅ |
| `/hidden-basic-auth/:user/:passwd` | ✅ | ✅ |
| `/digest-auth/:qop/:user/:passwd/:algorithm` | ❌ | ✅ |
| `/digest-auth/:qop/:user/:passwd` | ❌ | ✅ |
| `/bearer` | ✅ | ✅ |
| `/stream/:n` | ❌ | ✅ |
| `/delay/:n` | ✅ | ✅ |
| `/drip?numbytes=n&duration=s&delay=s&code=code` | ❌ | ✅ |
| `/range/:n?duration=s&chunk_size=code` | ❌ | ✅ |
| `/html` | ✅ | ✅ |
| `/robots.txt` | ✅ | ✅ |
| `/deny` | ✅ | ✅ |
| `/cache` | ✅ | ✅ |
| `/cache/:n` | ✅ | ✅ |
| `/etag/:etag` | ✅ | ✅ |
| `/bytes/:n` | ✅ | ✅ |
| `/stream-bytes/:n` | ❌ | ✅ |
| `/links/:n` | ✅ | ✅ |
| `/links/:n/:offset` | ✅ | ✅ |
| `/image` | ✅ | ✅ |
| `/image/png` | ✅ | ✅ |
| `/image/jpeg` | ✅ | ✅ |
| `/image/webp` | ✅ | ✅ |
| `/image/svg` | ✅ | ✅ |
| `/forms/post` | ✅ | ✅ |
| `/xml` | ✅ | ✅ |
| `/json` | ✅ | ✅ |
