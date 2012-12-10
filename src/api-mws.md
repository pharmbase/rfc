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
/info "GET" rdb

/* authenticated */
/auth/set "POST" key sum
/auth/del "POST" key sum
/link/set "POST" key sum
/link/del "POST" key sum
/link/get "POST" key sum
/data/add "POST" key sum
/data/lnk "POST" key sum
/data/get "POST" key sum
/name/get "POST" key sum
```

### `/ping` ###
Проверка доступности.
```
GET https://{addr}/ping HTTP/1.1
```
```
Content-Type: text/plain; charset=utf-8
200

PONG
```

### `/info` ###
Получение информации. Наличие параметра `rdb=info` укажет сервису дополнительно отобразить вывод команды Redis `INFO`.
```
GET https://{addr}/info HTTP/1.1
```
```
Content-Type: text/plain; charset=utf-8
200

Programming, Motherfucker

# Service
application:MWS
version:0.0.1
system:linux/amd64
golang:go1.0.3
timestamp:2012-10-26 13:41:07.671381 +0300 EEST
```

### `/auth/set` sysdba only ###
Установка параметров аутентификации.
```
POST https://{addr}/auth/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	{
		// Public key, string
		"KeyP": "ad0f7b32c41f311160db30fd2dc5f9f913f0aa41",
		// Secret key, string
		"KeyS": "f01fd7eb1485290c10b1ac95db9710670f89bda6"
	}
]
```
```
202
```

### `/auth/del` sysdba only ###
Удаление параметров аутентификации.
```
POST https://{addr}/auth/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
	// Public key, string
	"ad0f7b32c41f311160db30fd2dc5f9f913f0aa41"
]
```
```
202
```

### `/link/set` sysdba only (FIXME) ###
Установка параметров распознавания.
[`<hashtag>`][api-htag]
```
POST https://{addr}/link/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>

[
	{
		//
		"Hash": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
		//
		"Name": "В чащах юга жил бы цитрус? Да, но фальшивый экземпляр!",
		//
		"IDLink": "5577006791947779410",
		//
		"IDDrug": "9194777",
		//
		"IDBrnd": "0",
		//
		"IDCatg": "0"
	}
]
```
```
202
```

### `/link/del` sysdba only ###
Удаление параметров распознавания.
[`<hashtag>`][api-htag]
```
POST https://{addr}/link/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>

[
 	// Хеш-сумма SHA1 от наименования
 	"9e32295f8225803bb6d5fdfcc0674616a4413c1b"
]
```
```
202
```

### `/link/get` ###
Получение данных с инъекцией информации распознования.

[`<hashtag>`][api-htag]
```
POST https://{addr}/data/get?{auth} HTTP/1.1
```
```
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>
200

<BODY>
```


### `/data/add` ###
Добавление данных для распознования в зависимости от [типа технологического процесса][api-htag], значение которого следует передавать параметром в `Content-Type`. 

[`<hashtag>`][api-htag] [`<BODY>`][fmt-sale]
```
POST https://{addr}/data/add?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>

<BODY>
```
```
202
```

### `/data/lnk` ###
Получение информации распознавания для данных.

[`<hashtag>`][api-htag] (link.*)
```
POST https://{addr}/data/lnk?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>

{
	// Метаданные
	"Meta": {
		// Версия формата, int
		"Version": 1,
		// Агент передачи данных (программа) [*], string
		"Agent": "My Cool Application",
		// Время создания этого пакета данных, string[timestamp.nano]
		"Timestamp": "08.10.2012 00:00:00.001"
	},
	// Данные (массив заголовок/содержание)
	"Data": [{
			// Код препарата клиента, string[40]
			"Code": "10420",
			// Полное наименование препарата + производитель (через пробел), string[255]
			"Name": "Авамис аэр.27,5мкг/доза бал. 30д Глаксо Велком"
	}]
}
```
```
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>
200

{
	// Метаданные
	"Meta": {
		// Версия формата, int
		"Version": 1,
		// Агент передачи данных (программа) [*], string
		"Agent": "My Cool Application",
		// Время создания этого пакета данных, string[timestamp.nano]
		"Timestamp": "08.10.2012 00:00:00.001",
		
		// Отправитель (public key) [+], string
		"ISender": "f96b5d3726906aeb99fb6b2bc37f91a519cbc767",
		// Содержимое согласно проекта [+], string[proc]
		"IHashtag": "<hashtag>",
		// Время принятия этого пакета данных [+], string[timestamp.nano]
		"ITimestamp": "08.10.2012 00:57:28.403",
		// Уникальный идентификатор этого пакета данных на сервере [+], string
		"IHashstamp": "aeb99fb6b2bc37f91a519cbc767f96b5d3726906"
	},
	// Данные (массив заголовок/содержание)
	"Data": [{
			// Код препарата клиента, string[40]
			"Code": "10420",
			// Полное наименование препарата + производитель (через пробел), string[255]
			"Drug": "Авамис аэр.27,5мкг/доза бал. 30д Глаксо Велком",

			// Хеш-сумма наименования [+], string
			"IDrugSHA": "2fd4e1c67a2d28fced849ee1bb76e7391b93eb12",
			// Связь Морион для наименования [+], object
			"IDrugLNK": "FIXME"
	}]
}
```

### `/data/get` ###
Получение данных с инъекцией информации распознования.

[`<hashtag>`][api-htag] [`<BODY>`][fmt-sale]
```
POST https://{addr}/data/get?{auth} HTTP/1.1
```
```
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>
200

<BODY>
```

### `/name/get` sysdba only ###
Получение наименований для распознавания.

[`<hashtag>`][api-htag]
```
POST https://{addr}/name/get?{auth} HTTP/1.1
```
```
Content-Type: application/json; charset=utf-8; hashtag=<hashtag>
200

{
	// Метаданные
	"Meta": {
		// Версия формата, int
		"Version": 1,
		// Оригинальный источник данных (IHashstamp) [+], string
		"Origin": "eb99fb6b2bc37f91a519cbc767f96b5d3726906a",
		// Отправитель (public key) [+], string
		"Sender": "f96b5d3726906aeb99fb6b2bc37f91a519cbc767",
		// Содержимое согласно проекта [+], string[proc]
		"Hashtag": "<hashtag>",
		// Время подготовки этого пакета данных [+], string[timestamp.nano]
		"Timestamp": "08.10.2012 00:57:28.403",
		// Уникальный идентификатор этого пакета данных на сервере [+], string
		"Hashstamp": "aeb99fb6b2bc37f91a519cbc767f96b5d3726906"
	},
	// Данные (массив)
	"Data": [{
			// Полное наименование препарата + производитель (через пробел), string[255]
			"Drug": "Авамис аэр.27,5мкг/доза бал. 30д Глаксо Велком",
			// Хеш-сумма наименования [+], string
			"DrugSHA": "2fd4e1c67a2d28fced849ee1bb76e7391b93eb12",
			// Поставщик, string[1024]
			"Supp": "Рога и Копыта Корпорейшн",
			// Хеш-сумма поставщика [+], string
			"SuppSHA": "9e32295f8225803bb6d5fdfcc0674616a4413c1b"
	}]
}
```

[api-htag]: https://github.com/pharmbase/rfc/blob/master/src/api-htag.md
[fmt-sale]: https://github.com/pharmbase/rfc/blob/master/src/fmt-sale.md
