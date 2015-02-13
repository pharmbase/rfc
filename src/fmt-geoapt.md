Значения для параметра Morion-Skynet-Tag заголовока HTTP запроса:

```
	data.geoapt.ua // Данные за месяц из Украины
	data.geoapt.ru // Данные за месяц из России
```

Пример:

```
	Morion-Skynet-Tag: data.geoapt.ua
```

Формат приема-передачи данных:

```json
{
	"head": {
		"code": "string", // Код точки

		"head": "string", // 
		"name": "string",
		"addr": "string",
		"edrp": "string",

		"time_stamp": 0,
		"time_offset": 0,

		"link": {
			"code": "string",
			"hash": "string"
		}
		"info": {
			"key": "string",
			"tag": "string",
			"time": "string",
			"hash": "string"
		}

	},
	"body": [{
		"code": "string",
		"name": "string",
		"quant": 0.0,
		"price": 0.0,

		"link": {
			"code": "string",
			"hash": "string"
		}
	}]
}
```