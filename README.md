# oscript-httpbin
Cервис позволяющий локально тестировать HTTP клиент. Разработан на [OneScript](https://github.com/EvilBeaver/OneScript) + [WINOW](https://github.com/autumn-library/winow). Поддерживает бо́льшую часть эндпоинтов [httpbin.org](https://httpbin.org/).

* 1\. [Использование](#использование)
    * 1.1\. [Тестирование через asserts и 1connector](#тестирование-через-asserts-и-1connector)
    * 1.2\. [Приложение на фреймворке autumn](#приложение-на-фреймворке-autumn)
    * 1.3\. [CLI приложение](#cli-приложение)
* 2\. [Программный интерфейс](#программный-интерфейс)
* 3\. [Ограничения](#ограничения)
* 4\. [Сравнение с httpbin.org](#сравнение-с-httpbinorg)

## Использование

### Тестирование через [asserts](https://github.com/oscript-library/asserts) и [1connector](https://github.com/vbondarevsky/1connector)

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

### Приложение на фреймворке [autumn](https://github.com/autumn-library/autumn)

_МойЖелудь.os:_
``` bsl
#Использовать 1connector
#Использовать httpbin

&Пластилин 
Перем HttpBin;

Функция ВыполнитьДействие() Экспорт
    HttpBin.Запустить();
    Ответ = КоннекторHTTP.Get(HttpBin.URL() + "/get");
    HttpBin.Остановить();
КонецФункции

&Желудь
Процедура ПриСозданииОбъекта()
	
КонецПроцедуры
```

### CLI приложение

Запуск сервиса через команду **run**: `httpbin run`

Опции команды:</br>
`-h`, `--host` - имя хоста / IP адрес сервиса</br>
`-p`, `--port` - порт сервиса

## Программный интерфейс

### Класс `HttpBin`

По умолчанию сервис запускается по адресу 127.0.0.1:3334 фоновым заданием и с ожиданием инициализации сервиса.</br>
Класс реализован с текучим интерфейсом.

| Метод | Описание |
| --- | --- |
| `Запустить()` | Запускает сервис |
| `Остановить()` | Останавливает сервис |
| `URL()` | Возвращает адрес сервиса |
| `УстановитьПорт(<Порт>)` | Устанавливает порт сервиса |
| `УстановитьХост(<Хост>)` | Устанавливает хост сервиса |
| `ЗапускатьВФоне(<Флаг>)` | Запуск сервиса будет выполнен в фоновом режиме |
| `ОжидатьИнициализацию(<Флаг>)` | Ожидать инициализацию сервиса, запущенного в фоновом режиме |

## Ограничения

На данный момент нет поддержки https.

## Сравнение с httpbin.org

| Эндпоинт | oscript-httpbin | httpbin.org |
| --- | --- | --- |
| `/ip` | ✔️ | ✔️ |
| `/uuid` | ✔️ | ✔️ |
| `/uuid/:n` | ✔️ | ❌ |
| `/user-agent` | ✔️ | ✔️ |
| `/headers` | ✔️ | ✔️ |
| `/get` | ✔️ | ✔️ |
| `/post` | ✔️ | ✔️ |
| `/put` | ✔️ | ✔️ |
| `/patch` | ✔️ | ✔️ |
| `/delete` | ✔️ | ✔️ |
| `/anything` | ✔️ | ✔️ |
| `/anything/:anything` | ✔️ | ✔️ |
| `/base64/:value` | ✔️ | ✔️ |
| `/encoding/utf8` | ✔️ | ✔️ |
| `/gzip` | ❌ | ✔️ |
| `/deflate` | ❌ | ✔️ |
| `/brotli` | ❌ | ✔️ |
| `/status/:code` | ✔️ | ✔️ |
| `/response-headers?key=val` | ✔️ | ✔️ |
| `/redirect/:n` | ✔️ | ✔️ |
| `/redirect-to?url=foo` | ✔️ | ✔️ |
| `/redirect-to?url=foo&status_code=307` | ✔️ | ✔️ |
| `/relative-redirect/:n` | ✔️ | ✔️ |
| `/absolute-redirect/:n` | ✔️ | ✔️ |
| `/cookies` | ✔️ | ✔️ |
| `/cookies/set?name=value` | ✔️ | ✔️ |
| `/cookies/set/:name/:value` | ✔️ | ✔️ |
| `/cookies/delete?name` | ✔️ | ✔️ |
| `/basic-auth/:user/:passwd` | ✔️ | ✔️ |
| `/hidden-basic-auth/:user/:passwd` | ✔️ | ✔️ |
| `/digest-auth/:qop/:user/:passwd/:algorithm` | ❌ | ✔️ |
| `/digest-auth/:qop/:user/:passwd` | ❌ | ✔️ |
| `/bearer` | ✔️ | ✔️ |
| `/stream/:n` | ❌ | ✔️ |
| `/delay/:n` | ✔️ | ✔️ |
| `/drip?numbytes=n&duration=s&delay=s&code=code` | ❌ | ✔️ |
| `/range/:n?duration=s&chunk_size=code` | ❌ | ✔️ |
| `/html` | ✔️ | ✔️ |
| `/robots.txt` | ✔️ | ✔️ |
| `/deny` | ✔️ | ✔️ |
| `/cache` | ✔️ | ✔️ |
| `/cache/:n` | ✔️ | ✔️ |
| `/etag/:etag` | ✔️ | ✔️ |
| `/bytes/:n` | ✔️ | ✔️ |
| `/stream-bytes/:n` | ❌ | ✔️ |
| `/links/:n` | ✔️ | ✔️ |
| `/links/:n/:offset"` | ✔️ | ✔️ |
| `/image` | ✔️ | ✔️ |
| `/image/png` | ✔️ | ✔️ |
| `/image/jpeg` | ✔️ | ✔️ |
| `/image/webp` | ✔️ | ✔️ |
| `/image/svg` | ✔️ | ✔️ |
| `/forms/post` | ✔️ | ✔️ |
| `/xml` | ✔️ | ✔️ |
| `/json` | ✔️ | ✔️ |
