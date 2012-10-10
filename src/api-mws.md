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
  http[s]://{domain}/{service}/{version}/{aspect}/{action}[?params]

  ! Временный адрес:
  	http://10.0.1.116

  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле ответа запроса.

## Аутентификация ##

Аутентификация основана на хешировании пары уникальных строковых ключей "public key" и "secret key", которые однозначно идентифицируют клиента и вручаются ему перед работой с сервисом. 

По умолчанию "public key" = `sysdba`, а "secret key" = `masterkey`, следовательно при `sha1(sysdbamasterkey)` параметры для аутентификации будут записаны так:
  
  ```
  <auth> => key=sysdba&sum=f96b5d3726906aeb99fb6b2bc37f91a519cbc767
  ```

## Методы ##

API реализуется двумя методами HTTP: `GET` - для получения данных из сервиса в теле ответа на запрос и `POST` - для передачи данных в сервис в теле запроса.

```
Временный протокол:
https = http
Временный адрес в сети:
{addr} = 10.0.1.116
Аутентификация по умолчанию:
{auth} = key=sysdba&sum=f96b5d3726906aeb99fb6b2bc37f91a519cbc767
```

### `/ping` ###
```
GET https://{addr}/ping HTTP/1.1
```
```
PONG
```

### `/info` ###
```
GET https://{addr}/info HTTP/1.1
```
```
{
	"AppStr": "MWS",
	"AppVer": "0.0.1",
	"AppZen": "Programming, Motherfucker",
	"TypeOS": "linux/amd64",
	"MadeIn": "go1.0.3",
	"TimeOn": "2012-10-10 15:56:04.940722 +0300 EEST"
}
```

### `/auth/set` ###
```
POST https://{addr}/auth/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		/* Public key, string */
		"PKey": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41",
		/* Secret key, string */
		"SKey": "f01fd7eb1485290c10b1ac95db9710670f89bda6",
		/* Origin key, string */
		"OKey": "234567"
	}
]
```

### `/auth/del` ###
```
POST https://{addr}/auth/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		/* Public key, string */
		"PKey": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41"
	}
]
```

### `/link/set` ###
```
POST https://{addr}/link/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		"HSum": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
		"Name": "В чащах юга жил бы цитрус? Да, но фальшивый экземпляр!",
		"IDLink": "5577006791947779410",
		"IDDrug": "9194777",
		"IDBrnd": "0",
		"IDCatg": "0",
		"FIXME": "FIXME"
	}
]
```

### `/link/del` ###
```
POST https://{addr}/link/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
	 	/* Хеш-сумма SHA1 от наименования  */
	 	"HSum": "9e32295f8225803bb6d5fdfcc0674616a4413c1b"
	}
]
```

### `/link/get` ###
```
GET https://{addr}/link/get?{auth} HTTP/1.1
```
```
FIXME
```

### `/data/add` ###
```
POST https://{addr}/data/add?{auth}&mpr= HTTP/1.1
Content-Type: application/json; charset=utf-8

FIXME
```

### `/data/get` ###
```
GET https://{addr}/data/get?{auth}&mpr= HTTP/1.1
```
```
FIXME
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
GET http[s]://{domain/service/version}/info/get?<auth> 
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

#### `/auth/del` `202`
Удаляет аутентификацию по публичному ключу `key` (string).

#### `/link/set` `202`

Устанавливает для контрольной суммы `key` (string, sha1) ссылку на новое значение эталонного ключа `val` (string).

```
POST http[s]://{domain/service/version}/link/set?<auth> HTTP/1.1
Content-Type: application/json


```

#### `/link/del` `202`
	
Удаляет ссылку для контрольной суммы `key` (string, sha1).

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