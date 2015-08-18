# Инструкция по отправке данных клиентом в geoapteka.ua/ru с целью бронирования товара
*Этот файл поддерживается в формате [Markdown]*

## Соглашения
1. Протокол передачи данных [HTTP]

2. Формат приема-передачи данных [JSON] созданный в кодировке [UTF-8] без [BOM].

3. Общий url-формат:
  ```
  POST http://{domain}/{service}/{aspect}/{action}
  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле ответа запроса.

## Методы

### `/ping`
Проверка доступности аптеки для возможности бронирования
```
POST http://{addr}/booking/ping
```

Сообщение отправляется в следующем формате:
```json
{
    "id": "string" // Идентификатор торговой точки ("id_morion")
}
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X POST -T "ping.json" https://{addr}/booking/ping
```

В случае успеха будет получен ответ:
```
Content-Type: text/plain; charset=utf-8
200
```

В случае недоступности аптеки:
```
Content-Type: text/plain; charset=utf-8
204
```

### `/client_order`
Запрос на создание резерва в торговой точке
```
POST http://{addr}/booking/client_order
```

Первое сообщение отправляется в следующем формате:
```json
{
    "meta": {
        "id_shop": "string",  // Идентификатор торговой точки ("id_morion")
        "id_lang": "string",  // Идентификатор языка ("ru", "ua")

        "client_name": "string",   // Имя
        "client_phone": "string",  // Телефон
        "client_email": "string",  // Эл. почта
        "client_expiry": "string"  // Время истечение срока брони в формате RFC822
    },
    "data": [{
        "id": "string", // Идентификатор товара ("id_morion")
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]

}
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X POST -T "order.json" https://{addr}/booking/client_order
```

В случае успеха будет получен ответ в следующем формате:
```json
{
    "meta": {
        "id": "string",       // Идентификатор запроса
        "id_shop": "string",  // Идентификатор торговой точки (внутренний код)
        "id_lang": "string",  // Идентификатор языка ("ru", "ua")

        "client_name": "string",   // Имя
        "client_phone": "string",  // Телефон
        "client_email": "string",  // Эл. почта
        "client_expiry": "string", // Время истечение срока брони в формате RFC822

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

В случае, если поле "status_code" содержит "ERR", клиент может сделать повторный запрос в следующем формате:
```json
{
    "meta": {
        "id": "string",       // Идентификатор запроса ((!)полученный в предыдущем запросе(!))
        "id_shop": "string",  // Идентификатор торговой точки (внутренний код)
        "id_lang": "string",  // Идентификатор языка ("ru", "ua")

        "client_name": "string",   // Имя
        "client_phone": "string",  // Телефон
        "client_email": "string",  // Эл. почта
        "client_expiry": "string", // Время истечение срока брони в формате RFC822
    },
    "data": [{
        "id": "string", // Идентификатор товара ("id_morion")
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]

}
```

Возможны следующие ошибки:
* Сервис не в состоянии передать данные торговой точке:
```
Content-Type: text/plain; charset=utf-8
502
```
* Превышено время ожидания ответа от торговой точки:
```
Content-Type: text/plain; charset=utf-8
504
```

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTP]:https://ru.wikipedia.org/wiki/HTTP
[cURL]:https://ru.wikipedia.org/wiki/CURL
