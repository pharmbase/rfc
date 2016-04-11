# API бронирования данных в geoapteka.ua/ru для аптечных сетей
*Этот файл поддерживается в формате [Markdown]*

## Соглашения
1. Протокол передачи данных [HTTP] и [TCP]

2. Формат приема-передачи данных [JSON] созданный в кодировке [UTF-8] без [BOM].

3. Общий url-формат:
  ```
  POST http://{addr}/{aspect}/{user}/{action}
  ```

4. Коды состояния [стандартные](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes) и возвращаются всегда. Клиент имеет право на их интерпретацию. Ошибки могут быть дополнительно подробно описаны в теле ответа запроса.

## Подключение
1. Для получения данных, торговая точка подключается к очереди сообщений. В конкретном случае - [NATS]

2. Протокол получения данных с [NATS] - [TLS]

3. После успешного прохождения регистрации, торговая точка получает собственный канал для подключения, следующего формата:
  ```
  {aspect}.{id}
  ```
Пример канала:
   ```
  booking.854133
  ``` 

4. Запросы от клиентов будут приходить в следующем формате:
```
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")
        "id_user": "string"  // Идентификатор пользователя
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```
Пример запроса от клиента:
```
{
    "meta": {
        "id":      "hasdh1hkasdk123kasdaks",
        "id_shop": "500111",
        "id_lang": "ru",
        "id_user": "563881"
    },
    "data": [{
        "id": "56548",
        "quant": 2.0,
        "price": 15.40
    },{
        "id": "84655",
        "quant": 1.0,
        "price": 256.88
    },{
        "id": "1124",
        "quant": 2.0,
        "price": 56.08
    }]
}
```

## Работа с сервисом
После подключения к [NATS], все ответы от торговой точки посылаются на сервис посредством [TLS] соединения.

## Методы

### `/get_ch` (POST-метод)
Получение канала, торговой точки при включении, для прослушивания запросов от клиентов. 
```
POST http://{addr}/booking/apt/get_ch
```

Сообщение отправляется в следующем формате:
```
{
  "name": "string", // Название торговой точки
  "head": "string", // Название торговой сети (юр. лица)
  "addr": "string"  // Адрес торговой точки
}
```

Так же, при отправке обязательно указывается параметр заголовка *X-Morion-Skynet-Key*.

Значение параметра *X-Morion-Skynet-Key*:
* Устанавливается при подписании договора, уточняйте у вашего менеджера

Пример команды отправки файла с данными *data.json* при помощи служебной программы [cURL]:
```sh
curl -X POST -T "data.json" -H "X-Morion-Skynet-Key: xxxxxxxx" http://{addr}/booking/apt/get_topic
```

В случае успеха, будет получено имя канала для подключения к [NATS], в следующем формате:
```
{
  "ch_name":  "string", // Название канала для прослушивания
  "ch_reply": "string", // Название канала для отправки данных
  "login":    "string", // Логин для соединения
  "pass":     "string"  // Пароль для соединения
}
```

Возможны следующие коды состояния:
* `202` - канал выдан
* `204` - данной аптеки нет в базе данных

### `/request` (NATS-метод)
Отправка сервису данных о бронировании товара.

Отправка выполняется в следующем формате:
```
{
    "meta": {
        "id":      "string", // Идентификатор запроса
        "id_shop": "string", // Идентификатор торговой точки
        "id_lang": "string", // Идентификатор языка ("ru", "ua")
        "id_user": "string", // Идентификатор пользователя

        "apt_status_code": "string", // Статус обработки запроса ("ACCEPT", "REPEAT")
        "apt_order_exp":   "string", // Срок истечения брони в аптеке в формате ISO 8601 02-01-2006 15:04 (в случае успешного бронирования)
        "apt_order_num":   "string"  // Номер брони в аптеке (в случае успешного бронирования)
    },
    "data": [{
        "id": "string", // Идентификатор товара
        "quant": 0.0,   // Количество (float)
        "price": 0.0    // Цена (float)
    }]
}
```
Пример ответа от торговой точки:
```
{
    "meta": {
        "id":      "hasdh1hkasdk123kasdaks",
        "id_shop": "500111",
        "id_lang": "ru",
        "id_user": "563881",

        "apt_status_code": "ACCEPT",
        "apt_order_exp":   "31-09-2015 19:45",
        "apt_order_num":   "P-001"
    },
    "data": [{
        "id": "56548",
        "quant": 2.0,
        "price": 15.40
    },{
        "id": "84655",
        "quant": 1.0,
        "price": 156.88
    }]
}
```

Где статус код описывает текущее состояние брони:
* ACCEPT - заказ принят, бронь зарезервирована.
* REPEAT - запрос в аптеку не верен, повторите запрос с новыми данными (которые выдаются торговой точкой)
* UPDATE - бронь была изменена, в запросе отправляются новые данные о бронировании
* CANCEL - бронь отменили
* COMPLETE - забронированный товар был продан, в запросе так же отправляется перечень проданных товаров

Во всех вышеописанных случаях отправляется *полный набор данных*, **с внесенными в них изменениями и соответствующим статус кодом**.

После отправки данных, сервис отвечает клиенту "string"-форматом о статусе его запроса.
Возможны следующие статусы состояния при отправке данных на сервис:
* `ok` - данные успешно приняты и переданы клиенту
* `timeout` - клиент прервал соединение, либо аптека превысила время ожидания ответа
* `err` - ошибка на стороне сервера

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTP]:https://ru.wikipedia.org/wiki/HTTP
[TCP]:https://ru.wikipedia.org/wiki/TCP
[TLS]:https://ru.wikipedia.org/wiki/TLS
[cURL]:https://ru.wikipedia.org/wiki/CURL
[NATS]:http://nats.io