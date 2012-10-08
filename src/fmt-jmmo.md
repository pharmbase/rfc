# Формат электронных документов JSON MMO (Draft) #

Искомый формат предполагает возможным передачу нескольких электронных накладных одновременно. За основу взят формат [XMMO](http://pharmbase.com.ua/novyj-format-elektronnyx-dokumentov-xmmo/) `AS IS`.

* Формат [JSON](http://json.org/json-ru.html)
* Кодировка [UTF-8](http://ru.wikipedia.org/wiki/UTF-8)
* `//` - это комментарий только для пояснения и он должен отсутствовать в реальных данных
* `[*]` - эту пару ключ-значение можно пропустить 
* `string` - строка “в двойных кавычках”
* `string(date)` - строка в формате даты “dd.mm.yyyy hh:mm:ss”
* `number(int)` - число целое
* `number(float)` - число с плавающей точкой (разделитель точка “.”)

```
{
	  // Метаданные
	"Meta": {
		  // Версия формата
		"Version": number(int),
		  // Отправитель (public key)
		"Sender": string,
		  // Агент передачи данных [*]
		"Agent": string
	},
	  // Данные (массив заголовок/содержание)
	"Data": [{
		  // Заголовок
		"Head": {
			  // Источник данных (?)
			"Source": string,
			  // Уникальный идентификатор документа [*]
			"UID": string,
			  // Тип документа: «РасходнаяНакладная», «Прейскурант», «Заказ», «Потребность»
			"DocType": string,
			  // Тип распаковки для дробного прихода: «Десятичные», «Простые»
			"DivType": string,
			  // Дата документа
			"DocDate": string(date),
			  // Номер документа
			"DocNum": string,
			  // Комментарий [*]
			"UserNote": string,
			  // Идентификатор точки доставки (дислокации) [*]
			"LocationID": number(int),
			  // Название точки доставки (дислокации) [*]
			"LocationName": string,
			  // Адрес точки доставки [*]
			"LocationAdr": string,
			  // Сумма документа (без НДС)
			"DocSum": number(float),
			  // Сумма НДС документа (если документ без НДС, то «0»)
			"DocSumVat": number(float),
			  // Номер налоговой накладной [*]
			"DocNalogNum": number(int),
			  // Дата налоговой накладной [*]
			"DocNalogDate": string(date),
			  // Идентификатор вида оплаты. Актуально, если по контрагенту есть несколько соглашений [*]
			"PayID": string,
			  // Наименование вида оплаты [*]
			"PayName": string,
			  // Дата оплаты [*]
			"PayDate": string(date),
			  // Номер заказа [*]
			"OrderNum": [string],
			  // Наименование поставщика
			"SellerName": string,
			  // Код ЕГРПОУ поставщика
			"SellerERGPOU": number(int),
			  // Наименование покупателя
			"ClientName": string,
			  // Код ЕГРПОУ покупателя (плательщика)
			"ClientERGPOU": number(int),
			  // Номер договора [*]
			"Contract": string,
			  // Накладная подлежит регистрации (1 – подлежит, 0 – не подлежит) [*]
			"Reg": number(int)
		},
		  // Содержание (массив записей содержания)
		"Item": [{
			  // Идентификатор товара (код поставщика)
			"SellerID": string,
			  // Код Мориона [*]
			"MorionID": number(int),
			  // Наименование товара
			"DrugName": string,
			  // Наименование товара на украинском [*]
			"DrugNameUkr": string,
			  // Процент НДС
			"DrugVat": number(int),
			  // Название владельца лицензии [*]
			"OwnerName": string,
			  // Идентификатор владельца лицензии [*]
			"OwnerID": string,
			  // Название производителя
			"MakerName": string,
			  // Идентификатор производителя [*]
			"MakerID": string,
			  // Номер регистрации товара [*]
			"ItemRegNum": string,
			  // Дата регистрации товара [*]
			"ItemRegStart": string(date),
			  // Срок регистрации товара [*]
			"ItemRegEnd": string(date),
			  // Идентификатор товара во внешней БД (например: ГосНоменклатура или др.) [*]
			"ItemExtID": string,
			  // Заводской штрих-код [*]
			"Barcode": string,
			  // Украинский классификатор товара ВЭД [*]
			"HsCode": string,
			  // Распаковка [*]
			"Divisor": number(int),
			  // Принадлежность к ЦРГ (1 – принадлежит к ЦРГ,  0 – не принадлежит) [*]
			"Ord333": number(int),
			  // Акционный товар (1 – товар акционный, 0 – не акционный) [*]
			"Promotion": number(int),
			  // Идентификатор партии [*]
			"PartID": string,
			  // Идентификатор серии [*]
			"SeriesID": string,
			  // Представление серии [*]
			"SeriesName": string,
			  // Дата производства [*]
			"SerisesStart": string(date),
			  // Срок годности
			"SerisesEnd": string(date),
			  // Номер сертификата [*]
			"CertificateNum": string,
			  // Дата сертификата [*]
			"CertificateDate": string(date),
			  // Адрес zip-архива c картинками сертификата [*]
			"CertificateURL": string,
			  // Маркер заказа (поставщик получает маркер в заказе от аптеки и передает его в накладной) [*]
			"OrderMarker": string,
			  // Единица измерения (Строка)
			"Unit": string,
			  // Количество
			"Quant": number(float),
			  // Дробное количество – числитель (при простых дробях) [*]
			"QuantNum": number(int),
			  // Дробное количество – знаменатель (при простых дробях) [*]
			"QuantDiv": number(int),
			  // Цена таможенная или заводская
			"PriceFactory": number(float),
			  // Цена без НДС
			"Price": number(float),
			  // Цена НДС
			"PriceVat": number(float),
			  // Сумма без НДС
			"Sum": number(float),
			  // Сумма НДС
			"SumVat": number(float)
			}]
		}]
}
```
