Формат приема-передачи данных:

```
{
	"meta": {
		"id": "string",       // Идентификатор запроса
		"id_shop": "string",  // Идентификатор торговой точки (внутренний код)
		"id_lang": "string",  // Идентификатор языка ("ru", "ua")
		
		// Информация о клиенте
		"client_name": "string",   // Имя
		"client_phone": "string",  // Телефон
		"client_email": "string",  // Эл. почта
		"client_expiry": "string", // Время истечение срока брони

		// Добавляется аптекой
		"status_code": "string", // Статус обработки запроса ("OK", "ERR")
		"status_text": "string"  // Сообщение клиенту от аптеки
	},
	"data": [{
		"id": "string", // Идентификатор товара ("id_morion")
		"quant": 0.0,   // Количество (float)
		"price": 0.0    // Цена (float)
	}]
}
```
