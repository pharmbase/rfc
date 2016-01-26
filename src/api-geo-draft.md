# Draft

## `/reset-cart`
Запрос возвращает нормализованную на стороне сервера корзину пользователя. Корзина изменяется всегда в пределах одной искомой позиции `item`, значения для которой заполняются в зависимости от места инициации действия (под поиском, на точке или уже в корзине). 

В случае редактирования количества в меньшую сторону либо его полного удаления в `item` следует указывать отрицательный знак, а в количество передавать разницу изменения.

В случае успешного выполнения запроса будет возвращен массив предложений. Расклад товаров по точкам осуществляется всегда на стороне сервера.

```
curl -v -X POST \
	http://{domain}/reset-cart \
	-H 'Content-Type: application/json; charset=utf-8' \
	-d '{
			"coor": {
				"lat":0.0,
				"lon":0.0
			},
			"item": {
				"id_drug": "",
				"id_shop": "",
				"quant": 0.0,
				"price": 0.0
			},
			"cart": [
				{
					"id_drug": "",
					"id_shop": "",
					"quant": 0.0,
					"price": 0.0
				}
			]
		}'
```

Ответ, когда все получилось:
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

[
	{
		"id_drug": "",
		"id_shop": "",
		"quant": 0.0,
		"price": 0.0
	}
]
```

Ответ, когда что-то пошло не так на сервере:
```
HTTP/1.1 500 Internal Server Error
Content-Type: application/json; charset=utf-8

{
  "code": 500,
  "text": "message text"
}
```

Возможные коды `HTTP`:
```
200 OK
400 Bad Request 
500 Internal Server Error
```

## `/group-cart`

```
curl -v -X POST \
	http://{domain}/group-cart \
	-H 'Content-Type: application/json; charset=utf-8' \
	-d '{
		"coor": {
			"lat":0.0,
			"lon":0.0
		},
		"cart": [{
			"id_drug": "",
			"id_shop": "",
			"quant": 0.0,
			"price": 0.0
		}]
	}'
```

Ответ, когда все получилось:
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

[
	{
		"s": [
			{
				"i": "2691143",
				"b": false,
				"t": 3091,
				"l": "Optovih.png",
				"g": [
					51.492819,
					31.292319
				],
				"d": [
					{
						"i": "23601",
						"p": 4.44,
						"q": 344,
						"o": 1,
						"t": 1453811665,
						"l": "http://apteka911.com.ua/shop/analgin-tabl-500mg-10-darnitsa-prat-farm-firma-p22"
					}
				]
			}
		]
	}
]
```