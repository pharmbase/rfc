# Инструкция по отправке данных для публикации в geoapteka.ua/ru
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
        "code": "string"  // Код ЕДРПОУ (ОКПО)
    },
    "data": [{
        "id": "string",   // Идентификатор товара в торговой точке (внешний код)
        "name": "string", // Полное наименование препарата + производитель (через пробел)
        "quant": 0.0,     // Количество (остаток) (float, разделитель точка)
        "price": 0.0      // Цена розничная (float, разделитель точка)
    }]
}
```
Пример файла *data.json* с данными для 3 записей:
```json
{
    "Meta": {
        "id": "1",
        "head": "ТОВ \"ТЕСТ-Фарма\"",
        "name": "Аптека №24 (тест геоаптеки)",
        "addr": "г.Киев, ул.Тестовая,146/13",
        "code": "12345678"
    },
    "Data": [
        {
            "id": "1027",
            "name": "Абьюфен табл. 400мг №30 Бушара",
            "quant": 1,
            "price": 64.5
        },
        {
            "id": "1031",
            "name": "Авелокс табл. 400мг №5 Bayer",
            "quant": 5,
            "price": 464.02
        },
        {
            "id": "6432",
            "name": "Гиналгин табл. ваг. №10 ICN Polfa Rzeszow",
            "quant": 0.5,
            "price": 62.08
        }
    ]
}
```

## Отправка данных
Данные отправляются на сервер по протоколу [HTTPS] методом [POST], при этом следует обязательно указать параметры заголовка *X-Morion-Skynet-Tag* и *X-Morion-Skynet-Key*, ключ игнорирования сертификата *k*, а также рекомендуется указывать уникальное имя отправителя в *User Agent*.

Адрес отправки данных:
*  В Украине - https://skynet.morion.ua/data/add
*  В России - https://skynet.proximaresearch.ru/data/add

Значение параметра *X-Morion-Skynet-Tag*:
* В Украины - data.geoapt.ua
* В России - data.geoapt.ru

Значение параметра *X-Morion-Skynet-Key*:
* Устанавливается при подписании договора, уточняйте у вашего менеджера

Пример команды отправки файла с данными *data.json* в Украине при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.geoapt.ua" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.morion.ua/data/add"
```

Пример команды отправки файла с данными *data.json* в России при помощи служебной программы [cURL]:
```sh
curl -X POST -A "UniqUserAgent" -T "data.json" -H "X-Morion-Skynet-Tag: data.geoapt.ru" -H "X-Morion-Skynet-Key: xxxxxxxx" -k https://skynet.proximaresearch.ru/data/add"
```

Рекомендуется отправлять данные программно прямо из своих учетных систем при помощи доступных http-клиентов, минуя этап формирования файла с данными на жестком диске.

Максимальная периодичность отправки данных - один раз в час, минимальная - два раза в сутки. Максимальная продолжительность публикации данных без обновлений - 24 часа. Если данные не обновлялись за это время, то они будут автоматически удалены из публикации.

[Markdown]:https://ru.wikipedia.org/wiki/Markdown
[JSON]:http://json.org/json-ru.html
[UTF-8]:https://ru.wikipedia.org/w/index.php?title=UTF-8
[BOM]:https://ru.wikipedia.org/w/index.php?oldid=70741439
[HTTPS]:https://ru.wikipedia.org/wiki/HTTPS
[POST]:https://ru.wikipedia.org/wiki/POST_(HTTP)
[cURL]:https://ru.wikipedia.org/wiki/CURL
