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
GET http://{addr}/booking/cli/ping?id={id_shop}
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X GET http://{addr}/booking/cli/ping?id=500111
```

Возможны следующие коды состояния:
* `200` - аптека в сети
* `204` - аптека недоступна

### `/request`
Запрос на создание резерва в торговой точке.
```
POST http://{addr}/booking/cli/request
```

Пример команды при помощи служебной программы [cURL]:
```sh
curl -X POST -T "order.json" http://{addr}/booking/cli/request
```

Первое сообщение отправляется в следующем формате:
```
{
    "meta": {
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "cli_name":   "string", // Имя
        "cli_phone":  "string", // Телефон
        "cli_email":  "string", // Эл. почта
        "cli_expiry": "string"  // Время истечение срока брони в формате ISO 8601 02-01-2006 15:04
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

В случае успеха будет получен ответ в следующем формате:
```
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "cli_name":   "string", // Имя
        "cli_phone":  "string", // Телефон
        "cli_email":  "string", // Эл. почта
        "cli_expiry": "string", // Время истечение срока брони в формате ISO 8601 02-01-2006 15:04

        "apt_status_code": "string", // Статус обработки запроса ("ACCEPT", "REPEAT")
        "apt_status_text": "string", // Сообщение клиенту от аптеки
        "apt_num_order":   "string"  // Номер брони в аптеке (в случае успешного бронирования)
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

В случае, если поле "status_code" содержит "REPEAT", клиент может сделать повторный запрос, *обязательно указав при этом идентификатор запроса*, полученный ранее, в следующем формате:
```
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")

        "cli_name":   "string", // Имя
        "cli_phone":  "string", // Телефон
        "cli_email":  "string", // Эл. почта
        "cli_expiry": "string"  // Время истечение срока брони в формате ISO 8601 02-01-2006 15:04
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```

Возможны следующие статус коды:
* `202` - заказ принят
* `449` - требуется повторный запрос:
* `502` - сервис не в состоянии передать данные торговой точке:
* `504` - превышено время ожидания ответа от торговой точки:

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTP]:https://ru.wikipedia.org/wiki/HTTP
[cURL]:https://ru.wikipedia.org/wiki/CURL
