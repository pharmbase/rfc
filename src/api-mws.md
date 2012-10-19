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
/data/add "POST" key sum
/data/get "GET"  key sum
/name/get "GET"  key sum
```

### `/ping` ###
Проверка доступности.
```
GET https://{addr}/ping HTTP/1.1
```
```
PONG
```

### `/info/get` ###
Получение информации.
```
GET https://{addr}/info HTTP/1.1
```
```
{
	"AppStr": "MWS",
	"AppVer": "0.0.1",
	"AppZen": "Programming, Motherfucker",
	"System": "linux/amd64",
	"MadeIn": "go1.0.3",
	"TimeOn": "2012-10-10 15:56:04.940722 +0300 EEST"
}
```

### `/auth/set` sysdba only ###
Установка параметров аутентификации.
```
POST https://{addr}/auth/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		/* Public key, string */
		"KeyP": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41",
		/* Secret key, string */
		"KeyS": "f01fd7eb1485290c10b1ac95db9710670f89bda6"
	}
]
```

### `/auth/del` sysdba only ###
Удаление параметров аутентификации.
```
POST https://{addr}/auth/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		/* Public key, string */
		"KeyP": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41"
	}
]
```
<!--
### `/link` ###
```
GET https://{addr}/link/get?{auth} HTTP/1.1
```
```
FIXME
```
-->
### `/link/set` sysdba only (FIXME) ###
Установка параметров распознавания.
```
POST https://{addr}/link/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		"Hash": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
		"Name": "В чащах юга жил бы цитрус? Да, но фальшивый экземпляр!",
		"IDLink": "5577006791947779410",
		"IDDrug": "9194777",
		"IDBrnd": "0",
		"IDCatg": "0"
	}
]
```

### `/link/del` sysdba only ###
Удаление параметров распознавания.
```
POST https://{addr}/link/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
	 	/* Хеш-сумма SHA1 от наименования  */
	 	"Hash": "9e32295f8225803bb6d5fdfcc0674616a4413c1b"
	}
]
```
<!--
### `/data` ###
```
GET https://{addr}/data/get?{auth}&{project} HTTP/1.1
```
```
FIXME
```
-->
### `/data/add` ###
Добавление данных для распознования в зависимости от [типа технологического процесса](https://github.com/pharmbase/rfc/blob/master/src/api-proc.md), значение которого следует передавать параметром в `Content-Type`. 

[`<BODY>`](https://github.com/pharmbase/rfc/blob/master/src/fmt-sale.md)
```
POST https://{addr}/data/add?{auth}&{project} HTTP/1.1
Content-Type: application/json; charset=utf-8; process=sale-out.daily

<BODY>
```
