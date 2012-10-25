# Morion Web Services API (Draft) #

*This document is a work in progress. The API is not stable.*

<!--
Облачный сервис распознавания входящих данных различного типа в реальном режиме времени. Нераспознанные названия отсылает в сервис экспертной системы для последующей их привязки к значениям ключей эталонных справочников. Распознанные названия вместе с их атрибутами отсылает для последующей обработки в соотвествующие сервисы.
-->

## Соглашения ##

1. Протокол передачи данных [HTTPS](http://ru.wikipedia.org/wiki/HTTPS).

2. Формат приема-передачи данных [JSON](http://json.org/).

3. Общий url-формат:

  ```
  GET  http[s]://{domain}/{service}/{version}/{aspect}/[?params]
  POST http[s]://{domain}/{service}/{version}/{aspect}/{action}[?params]

  ! Временный адрес:
  	http://10.0.1.116

  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле ответа запроса.

## Аутентификация ##

Аутентификация основана на хешировании пары уникальных строковых ключей "public key" и "secret key", которые однозначно идентифицируют источник передачи данных и вручаются ему перед работой с сервисом. 

По умолчанию "public key" = `sysdba`, а "secret key" = `masterkey`, следовательно при `sha1(sysdbamasterkey)` параметры для аутентификации будут записаны так:
  
  ```
  <auth> => key=sysdba&sum=f96b5d3726906aeb99fb6b2bc37f91a519cbc767
  ```

## Методы ##

API реализуется двумя методами HTTP: `GET` - для получения данных из сервиса в теле ответа на запрос и `POST` - для передачи данных в сервис в теле запроса.

```
Временный адрес в сети:
{addr} = 10.0.1.116
Аутентификация по умолчанию:
{auth} = key=sysdba&sum=f96b5d3726906aeb99fb6b2bc37f91a519cbc767

/ping "GET"
/info "GET"

/* authenticated */
/auth/set "POST" key sum
/auth/del "POST" key sum
/link/set "POST" key sum
/link/del "POST" key sum
/link/get "POST" key sum
/data/add "POST" key sum
/data/get "GET"  key sum
/name/get "GET"  key sum
```

### `/ping` ###
Проверка доступности.
```
GET https://{addr}/ping HTTP/1.1
```
[Response][fmt-pong]

### `/info` ###
Получение информации.
```
GET https://{addr}/info HTTP/1.1
```
[Response][fmt-info]

### `/auth/set` sysdba only ###
Установка параметров аутентификации.
```
POST https://{addr}/auth/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<BODY>
```
[`<BODY>`][fmt-auth]

### `/auth/del` sysdba only ###
Удаление параметров аутентификации.
```
POST https://{addr}/auth/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<BODY>
```
[`<BODY>`][fmt-auth]

### `/link/set` sysdba only (FIXME) ###
Установка параметров распознавания.
```
POST https://{addr}/link/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<BODY>
```
[`<BODY>`][fmt-link]

### `/link/del` sysdba only ###
Удаление параметров распознавания.
```
POST https://{addr}/link/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<BODY>
```
[`<BODY>`][fmt-link]

### `/link/get` (FIXME) ###
Получение параметров распознавания.
```
POST https://{addr}/link/get?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<FIXME>
```

<!--
### `/data/get` ###
```
GET https://{addr}/data/get?{auth}&{project} HTTP/1.1
```
```
FIXME
```
-->
### `/data/add` ###
Добавление данных для распознования в зависимости от [типа технологического процесса][api-proc], значение которого следует передавать параметром в `Content-Type`. 

```
POST https://{addr}/data/add?{auth}&{project} HTTP/1.1
Content-Type: application/json; charset=utf-8; process=sale-out.daily

<BODY>
```
[`<process>`][api-proc]
[`<BODY>`][fmt-sale]

### `/data/get` (FIXME) ###
Получение данных с инъекцией информации распознования.
```
POST https://{addr}/data/get?{auth} HTTP/1.1
```

### `/name/get` sysdba only ###
Получение наименований для распознавания.
```
POST https://{addr}/name/get?{auth} HTTP/1.1
```
[Response][fmt-name]


[api-proc]: https://github.com/pharmbase/rfc/blob/master/src/api-proc.md
[fmt-pong]: https://github.com/pharmbase/rfc/blob/master/src/fmt-pong.md
[fmt-info]: https://github.com/pharmbase/rfc/blob/master/src/fmt-info.md
[fmt-auth]: https://github.com/pharmbase/rfc/blob/master/src/fmt-auth.md
[fmt-link]: https://github.com/pharmbase/rfc/blob/master/src/fmt-link.md
[fmt-name]: https://github.com/pharmbase/rfc/blob/master/src/fmt-name.md
[fmt-sale]: https://github.com/pharmbase/rfc/blob/master/src/fmt-sale.md
