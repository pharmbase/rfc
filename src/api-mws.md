# Morion Web Services API (Draft) #

* This document is a work in progress. The API is not stable. *

<!--
Облачный сервис распознавания входящих данных различного типа в реальном режиме времени. Нераспознанные названия отсылает в сервис экспертной системы для последующей их привязки к значениям ключей эталонных справочников. Распознанные названия вместе с их атрибутами отсылает для последующей обработки в соотвествующие сервисы.
-->

## Соглашения ##

1. Протокол передачи данных [HTTPS](http://ru.wikipedia.org/wiki/HTTPS).

2. Формат приема-передачи данных [JSON](http://json.org/).

3. Общий url-формат:

  ```
  http[s]://{domain}/{service}/{version}/{aspect}/{action}[?<auth>&key=val ...]

  ! Временный адрес:
  	http://10.0.1.116

  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле запроса.

## Аутентификация ##

Аутентификация основана на хешировании пары уникальных строковых ключей "public key" и "secret key", которые однозначно идентифицируют клиента и вручаются ему перед работой с сервисом. 

По умолчанию "public key" = `sysdba`, а "secret key" = `masterkey`, следовательно при `sha1(sysdbamasterkey)` параметры для аутентификации будут записаны так:
  
  ```
  <auth> => key=sysdba&sum=f96b5d3726906aeb99fb6b2bc37f91a519cbc767
  ```

## Методы ##

API реализуется двумя методами HTTP: `GET` - для получения данных из сервиса в теле ответа на запрос и `POST` - для передачи данных в сервис в теле запроса.

### `/ping` ###

```
GET http[s]://{address}/ping
```

```
PONG
```

### `/info` ###

```
GET http[s]://{address}/info
```

```
{
	"AppStr": "MWS",
	"AppVer": "0.0.1",
	"AppZen": "Programming, Motherfucker",
	"TypeOS": "linux/amd64",
	"MadeIn": "go1.0.3",
	"TimeOn":"2012-10-10 15:56:04.940722 +0300 EEST"
}
```

<!--
```
{domain/service/version} => 10.0.1.116/linkdroid/v1
/info/{action} - Информация
/auth/{action} - Аутентификация
/link/{action} - Связь
/data/{action} - Входящие данные
```
#### `/info/get`  `200`

Отдает различную информацию. Например, название и версию сервиса, которая формируется по [соглашениям](http://semver.org/).

```
GET http[s]://{domain/service/version}/info/get?<auth> HTTP/1.1
```

```
{
	"AppStr":"Linkdroid",
	"AppVer":"0.1.1",
	"MadeIn":"go1.0.2",
	"TypeOS":"linux/amd64"
}
```
#### `/auth/set` `202`

Создает или обновляет аутентификацию как пару хеш-ключей - публичного `key` (string, sha1) и секретного `val` (string).

Значения для ключей `key` и `val` должны быть уникальны и расчитаны по формуле `sha1(guid)`. Каждая пара значений `key` и `val` сопоставляется в экспертной системе только с одной организацией (клиентом). Механизм распростанения ключей среди клиентов сервиса разрабатывается отдельно соотвествующими подразделениями.


```
POST http[s]://{domain/service/version}/auth/set?<auth> HTTP/1.1
Content-Type: application/json

[
	{
		"Key": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41",
		"Val": "f01fd7eb1485290c10b1ac95db9710670f89bda6"
	}
]
```

#### `/auth/del` `202`
Удаляет аутентификацию по публичному ключу `key` (string).

```
POST http[s]://{domain/service/version}/auth/del?<auth> HTTP/1.1
Content-Type: application/json

[
	{
		"Key": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41"
	}
]
```
#### `/link/add` `202`

Отправляет хеш строки `key` (string, sha1) и ее значение `val` (string) в вебсервис для экспертного связывания, тип проекта указывается в параметре запроса `way`.


```
POST http[s]://{domain/service/version}/link/add/?way=01 HTTP/1.1
Content-Type: application/json

[
	{
		"Key": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
		"Val": "В чащах юга жил бы цитрус? Да, но фальшивый экземпляр!"
	}
]
```

#### `/link/set` `202`

Устанавливает для контрольной суммы `key` (string, sha1) ссылку на новое значение эталонного ключа `val` (string).

```
POST http[s]://{domain/service/version}/link/set?<auth> HTTP/1.1
Content-Type: application/json

[
	{
		"Key": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
		"Val": "5577006791947779410"
	}
]
```

#### `/link/del` `202`
	
Удаляет ссылку для контрольной суммы `key` (string, sha1).

```
POST http[s]://{domain/service/version}/link/del?<auth> HTTP/1.1
Content-Type: application/json

[
	{
	 	"Key": "9e32295f8225803bb6d5fdfcc0674616a4413c1b"
	}
]
```
#### `/data/add` `202`

Принимает данные из различных источников. Формат и логика их последующей обработки зависит от параметра `way`, который задается разработчиками сервиса для каждого отдельного случая.

`way=01` - например, чек (как один документ) из аптеки в `json`.


```
POST http[s]://{domain/service/version}/data/add?<auth>&way=01 HTTP/1.1
Content-Type: application/json

{

}
```
-->