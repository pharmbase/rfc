# API бронирования данных в geoapteka.ua/ru
*Этот файл поддерживается в формате [Markdown]*

## Соглашения
1. Протокол передачи данных [HTTP]

2. Формат приема-передачи данных [JSON] созданный в кодировке [UTF-8] без [BOM].

3. Общий url-формат:
  ```
  POST http://{addr}/{aspect}/{user}/{action}
  GET  http://{addr}/{aspect}/{user}/{action}
  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле ответа запроса.

## Методы

### `/ping`
Проверка доступности аптеки для возможности бронирования.
```
GET http://{addr}/booking/cli/ping?id={shop_id}
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X GET http://{addr}/booking/cli/ping?id=500111
```

В случае успеха будет получен код состояния:
```
200
```

В случае недоступности аптеки:
```
204
```

### `/request_box`
Запрос на создание резерва в торговой точке.
```
POST http://{addr}/booking/cli/request_box
```

Первое сообщение отправляется в следующем формате:
```json
{
    "meta": {
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "client_name":   "string", // Имя
        "client_phone":  "string", // Телефон
        "client_email":  "string", // Эл. почта
        "client_expiry": "string"  // Время истечение срока брони в формате RFC3339
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X POST -T "order.json" http://{addr}/booking/cli/request_box
```

В случае успеха будет получен ответ в следующем формате:
```json
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "client_name":   "string", // Имя
        "client_phone":  "string", // Телефон
        "client_email":  "string", // Эл. почта
        "client_expiry": "string", // Время истечение срока брони в формате RFC3339

        "status_code": "string", // Статус обработки запроса ("OK", "REPEAT")
        "status_text": "string", // Сообщение клиенту от аптеки
        "id_order":    "string"  // Номер брони в аптеке (в случае успешного бронирования)
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

В случае, если поле "status_code" содержит "REPEAT", клиент может сделать повторный запрос, обязательно указав при этом идентификатор запроса, полученный ранее, в следующем формате:
```json
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "client_name":   "string", // Имя
        "client_phone":  "string", // Телефон
        "client_email":  "string", // Эл. почта
        "client_expiry": "string"  // Время истечение срока брони в формате RFC3339
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

Возможны следующие статус коды:
* Заказ принят:
```
202
```
* Требуется повторный запрос:
```
449
```
* Сервис не в состоянии передать данные торговой точке:
```
502
```
* Превышено время ожидания ответа от торговой точки:
```
504
```

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTP]:https://ru.wikipedia.org/wiki/HTTP
[cURL]:https://ru.wikipedia.org/wiki/CURL
