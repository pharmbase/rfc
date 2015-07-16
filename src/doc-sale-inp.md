# Инструкция по отправке sale-in данных
*Этот файл поддерживается в формате [Markdown]*

## Подготовка данных
Файл с данными должен удовлетворять спецификации формата [JSON] и быть создан в кодировке [UTF-8] без [BOM].

Спецификация структуры данных:
```json
{
    "meta": {
        "id": "string",   // Идентификатор торговой точки (внешний код)
        "name": "string", // Название торговой точки
        "head": "string", // Название торговой сети (юр. лица)
        "addr": "string", // Адрес торговой точки
        "code": "string", // Код ЕДРПОУ (ОКПО)
        "span": ["", ""]  // Временной диапазон строкой ISO 8601 (RFC3339) 2015-02-17T01:15:25+02:00
    },
    "data": [{
        "id": "string",   // Идентификатор товара в торговой точке (внешний код)
        "name": "string", // Товар, полное наименование препарата + производитель (через пробел)
        "quant": 0.0,     // Количество прихода (float)
        "price": 0.0,     // Цена расхода (float)
        "balance": 0.0,   // Остаток на конец периода (float)
        "supp": "string", // Поставщик (название)
        "code": "string"  // Код ЕДРПОУ (ОКПО)
    }]
}
```

Пример файла *data.json* с данными из 3 позиций:
```json
{
    "Meta": {
        "id": "1",
        "head": "ТОВ \"ТЕСТ-Фарма\"",
        "name": "Аптека №24 (тест геоаптеки)",
        "addr": "г.Киев, ул.Тестовая,146/13",
        "code": "12345678",
        "span": ["2015-02-17T01:15:25+02:00", "2015-02-19T01:15:25+02:00"] // Временной диапазон строкой ISO 8601 (RFC3339) 2015-02-17T01:15:25+02:00
    },
    "Data": [
        {
            "id": "1027",
            "name": "Абьюфен табл. 400мг №30 Бушара",
            "quant": 1,
            "price": 64.5,
            "balance": 10.0,
            "supp": "Артель Рога и Копыта",
            "code": "64747"
        },
        {
            "id": "1031",
            "name": "Авелокс табл. 400мг №5 Bayer",
            "quant": 5,
            "price": 464.02
            "balance": 14.0,
            "supp": "Инокентий и Ко",
            "code": "5678"
        },
        {
            "id": "6432",
            "name": "Гиналгин табл. ваг. №10 ICN Polfa Rzeszow",
            "quant": 0.5,
            "price": 62.08,
            "balance": 100.0,
            "supp": "Артель Рога и Копыта",
            "code": "64747"
        }
    ]
}
```

## Отправка данных
Данные отправляются на сервер по протоколу [HTTPS] методом [POST], при этом следует обязательно указать параметры заголовка *X-Morion-Skynet-Tag* и *X-Morion-Skynet-Key*, ключ игнорирования сертификата *k*, а также рекомендуется указывать уникальное имя отправителя в *User Agent*.

Адрес отправки данных:
*  В Украине -    https://skynet.morion.ua/data/add
*  В Беларуссии - https://skynet.morion.ua/data/add
*  В России -     https://skynet.proxima-research.ru/data/add
*  В Казахстане - https://skynet.morion.ua/data/add

Значение параметра *X-Morion-Skynet-Tag*:
* В Украине -     data.sale-out.monthly.ua | data.sale-out.weekly.ua | data.sale-out.daily.ua
* В Беларуссии-   data.sale-out.monthly.by | data.sale-out.weekly.by | data.sale-out.daily.by
* В России -      data.sale-out.monthly.ru | data.sale-out.weekly.ru | data.sale-out.daily.ru
* В Казахстане -  data.sale-out.monthly.kz | data.sale-out.weekly.kz | data.sale-out.daily.kz

Значение параметра *X-Morion-Skynet-Key*:
* Устанавливается при подписании договора, уточняйте у вашего менеджера

Пример команды отправки файла с данными *data.json* в Украине при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.sale-out.daily.ua" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.morion.ua/data/add
```

Пример команды отправки файла с данными *data.json* в Беларуссии при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.sale-out.daily.by" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.morion.ua/data/add
```

Пример команды отправки файла с данными *data.json* в России при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.sale-out.daily.ru" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.proxima-research.ru/data/add
```

Пример команды отправки файла с данными *data.json* в Казахстане при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.sale-out.daily.kz" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.morion.ua/data/add
```

Рекомендуется отправлять данные программно прямо из своих учетных систем при помощи доступных http-клиентов, минуя этап формирования файла с данными на жестком диске.


[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTPS]:https://ru.wikipedia.org/wiki/HTTPS
[POST]:https://ru.wikipedia.org/wiki/POST_(HTTP)
[cURL]:https://ru.wikipedia.org/wiki/CURL
