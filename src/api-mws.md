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
```
PONG
```
```
200
```

### `/info` ###
Получение информации.
```
GET https://{addr}/info HTTP/1.1
```
```
Programming, Motherfucker

# Service
application:MWS
version:0.0.1
system:linux/amd64
golang:go1.0.3
timestamp:2012-10-26 13:41:07.671381 +0300 EEST

=== Redis INFO ===

# Server
redis_version:2.6.0
redis_git_sha1:00000000
redis_git_dirty:0
redis_mode:standalone
os:Linux 3.5.0-17-generic x86_64
arch_bits:64
multiplexing_api:epoll
gcc_version:4.7.2
process_id:11432
run_id:1320afcae313927f9e50739a74cf40f6e8d0ab72
tcp_port:6379
uptime_in_seconds:89123
uptime_in_days:1
lru_clock:907078

# Clients
connected_clients:1
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0

# Memory
used_memory:530416
used_memory_human:517.98K
used_memory_rss:2154496
used_memory_peak:644984
used_memory_peak_human:629.87K
used_memory_lua:31744
mem_fragmentation_ratio:4.06
mem_allocator:jemalloc-3.0.0

# Persistence
loading:0
rdb_changes_since_last_save:48
rdb_bgsave_in_progress:0
rdb_last_save_time:1351247796
rdb_last_bgsave_status:ok
rdb_last_bgsave_time_sec:0
rdb_current_bgsave_time_sec:-1
aof_enabled:0
aof_rewrite_in_progress:0
aof_rewrite_scheduled:0
aof_last_rewrite_time_sec:-1
aof_current_rewrite_time_sec:-1
aof_last_bgrewrite_status:ok

# Stats
total_connections_received:79
total_commands_processed:4130
instantaneous_ops_per_sec:0
rejected_connections:0
expired_keys:0
evicted_keys:0
keyspace_hits:2295
keyspace_misses:834
pubsub_channels:0
pubsub_patterns:0
latest_fork_usec:241

# Replication
role:master
connected_slaves:0

# CPU
used_cpu_sys:160.72
used_cpu_user:216.49
used_cpu_sys_children:0.00
used_cpu_user_children:0.00

# Keyspace
db0:keys=3,expires=0
```
```
200
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
```
POST https://{addr}/link/set?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

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
```
POST https://{addr}/link/del?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

[
 	// Хеш-сумма SHA1 от наименования
 	"9e32295f8225803bb6d5fdfcc0674616a4413c1b"
]
```
```
202
```

### `/link/get` (FIXME) ###
Получение параметров распознавания.
```
POST https://{addr}/link/get?{auth} HTTP/1.1
Content-Type: application/json; charset=utf-8

<FIXME>
```
```
200
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

[`<process>`][api-proc] [`<BODY>`][fmt-sale]
```
POST https://{addr}/data/add?{auth}&{project} HTTP/1.1
Content-Type: application/json; charset=utf-8; process=sale-out.daily

<BODY>
```
```
202
```

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
```
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
		"Process": "sale-out.daily",
		// Время подготовки этого пакета данных [+], string[timestamp.nano]
		"Timestamp": "08.10.2012 00:57:28.403",
		// Уникальный идентификатор этого пакета данных на сервере [+], string
		"Hashstamp": "aeb99fb6b2bc37f91a519cbc767f96b5d3726906"
	},
	// Данные (массив заголовок/содержание)
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

[api-proc]: https://github.com/pharmbase/rfc/blob/master/src/api-proc.md
[fmt-sale]: https://github.com/pharmbase/rfc/blob/master/src/fmt-sale.md
