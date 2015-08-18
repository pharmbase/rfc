# Инструкция по отправке данных в geoapteka.ua аптечными сетями
*Этот файл поддерживается в формате [Markdown]*

## Подготовка данных
Файл с данными должен удовлетворять спецификации формата [JSON] и быть создан в кодировке [UTF-8] без [BOM].

## Регистрация точки при подключении
Спецификация структуры данных:
```json
{
    "name": "string", // Название торговой точки
    "head": "string", // Название торговой сети (юр. лица)
    "addr": "string"  // Адрес торговой точки
}
```

В случае успешной регистрации будет получен ответ в следующем формате:
```json
{
    "topic_name": "string" // Адрес канала для подключения
}
```

## Работа с данными
После подключения, общение с сервисом происходит в следующем формате:
```json
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

        // Добавляется аптекой в качестве ответа
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

Пример файла *data.json* с данными из 3 позиций:
```json
{
    "Meta": {
        "id": "jjasdjasd123jasdaasd",       
        "id_shop": "800564",  
        "id_lang": "ru", 

        "client_name": "Иван Иванович Иванов",  
        "client_phone": "+380631112233",  
        "client_email": "test@testmail.com",  
        "client_expiry": "10-11-2015,15:40", 

        "status_code": "Ok",
        "status_text": "Спасибо за то, что выбрали нас. Вас заказ Р-001"  
    },
    "Data": [
        {
            "id": "1027",
            "quant": 1,
            "price": 64.5
        },
        {
            "id": "1031",
            "quant": 5,
            "price": 464.02
        },
        {
            "id": "6432",
            "quant": 0.5,
            "price": 62.08
        }
    ]
}
```

## Поддержка соеденения
Для успешной поддержки соединения, необходима в n-времени, периодичная отправка данных следующего содержания:
```json
{
    "topic_name": "string" // Адрес канала, выданный точке сервисом
}
```

## Завершение работы
В случае штатного завершения работы, на сервис отправляются данные следующего содержания:
```json
{
    "topic_name": "string" // Адрес канала, выданный точке сервисом
}
```

## Отправка данных
Данные отправляются на сервер по протоколу [HTTPS] методом [POST], при этом, при регистрации, следует обязательно указать параметры заголовка *X-Morion-Skynet-Key* и ключ игнорирования сертификата *k*

Адрес отправки данных:
*  При регистрации:           - https://skynet.morion.ua/geoapt/register
*  При отправке данных:       - https://skynet.morion.ua/geoapt/api_manager
*  При поддержке соединения : - https://skynet.morion.ua/geoapt/ping
*  При завершении работы:     - https://skynet.morion.ua/geoapt/unregister

Значение параметра *X-Morion-Skynet-Key*:
* Устанавливается при подписании договора, уточняйте у вашего менеджера

Пример команды отправки файла с данными *register.json* в Украине при помощи служебной программы [cURL]:
```sh
curl -X POST -T "register.json" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.morion.ua/geoapt/register
```

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTPS]:https://ru.wikipedia.org/wiki/HTTPS
[POST]:https://ru.wikipedia.org/wiki/POST_(HTTP)
[cURL]:https://ru.wikipedia.org/wiki/CURL
