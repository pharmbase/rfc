# Формат приемо-передачи данных `sale-in|out` для PharmXplorer (Draft) #

Искомый формат предполагает возможность передачи данных о [приходах/расходах/остатках][0] из одной или нескольких точек за определенный временной интервал. Формат обеспечивает требования текущей архитектуры обработки данных. Пары ключ-значение, для которых не существует значения (0 для чисел или пустые строки) должны быть исключены при формировании реальных выходных данных.

Спецификация-пример (1 точка, 1 наименование):

* Формат [JSON](http://json.org/json-ru.html)
* Кодировка [UTF-8](http://ru.wikipedia.org/wiki/UTF-8)
* `//` - это комментарий только для пояснения и он должен отсутствовать в реальных данных
* `[*]` - эту пару ключ-значение можно пропустить
* `[+]` - эту пару ключ-значение вносит сервис распознавания и ее не надо формировать на передающей стороне
* `string, int, float, bool` - типы данных: “строка в двойных кавычках”, целое, число с плавающей точкой (разделитель точка “.”), логический (true|false)
* `string[timestamp]` - дата и время строкой в формате"dd.mm.yyyy hh:mm:ss"
* `string[timestamp.nano]` - дата и время строкой в формате"dd.mm.yyyy hh:mm:ss.zzzzzzzzz"

```json
{
	// Метаданные
	"Meta": {
		// Версия формата, int
		"Version": 1,
		// Агент передачи данных (программа) [*], string
		"Agent": "My Cool Application",
		// Время создания этого пакета данных, string[timestamp.nano]
		"Timestamp": "08.10.2012 00:00:00.001",
		// Нижняя граница временного диапазона отбора данных, string[timestamp]
		"TRangeLower": "08.10.2012 00:00:00",
		// Верхняя граница временного диапазона отбора данных, string[timestamp]
		"TRangeUpper": "08.10.2012 23:59:59",
		// Учетность данных [*], string[analytic|synthetic]
		"AccountType": "analytic",
		

		// << !!! reserved for internal use only
		// Отправитель (public key) [+], string
		"ISender": "f96b5d3726906aeb99fb6b2bc37f91a519cbc767",
		// Содержимое согласно проекта [+], string
		"IHashtag": "<hashtag>",
		// Время принятия этого пакета данных [+], string[timestamp.nano]
		"ITimestamp": "08.10.2012 00:57:28.403",
		// Уникальный идентификатор этого пакета данных на сервере [+], string
		"IHashstamp": "aeb99fb6b2bc37f91a519cbc767f96b5d3726906",
		// Контрольная сумма массива Data[] [+], string
		"IHashcheck": "a519cbc767f96b5d3726906aeb99fb6b2bc37f91"
		// >>
	},
	// Данные (массив заголовок/содержание)
	"Data": [{
		// Заголовок
		"Head": {
			// Источник данных (BR_NICK), string[20]
			"Source": "21-00-0540-А",
			// Источник данных (MDS LICENSE), string[30]
			"MDSLns": "LGMB-4HM7-6KWW-OGM4"
		},
		// Содержание (массив записей содержания)
		"Item": [{
			// Код препарата клиента, string[40]
			"Code": "10420",
			// Полное наименование препарата + производитель (через пробел), string[255]
			"Drug": "Авамис аэр.27,5мкг/доза бал. 30д Глаксо Велком",
			

			// << sale-in must
			// Поставщик, string[1024]
			"Supp": "Рога и Копыта Корпорейшн",
			// ОКПО (оно же ЕГРПОУ), string[12]
			"SuppOKPO": "82389009",
			// >>


			// << sale-in must, sale-out maybe
			// Количество прихода float
			"QuantInp": 20,
			// Цена прихода (за единицу товара, float
			"PriceInp": 32.50,
			// >>
			

			// << sale-out must
			// Количество расхода float
			"QuantOut": 3,
			// Цена расхода (за единицу товара), float
			"PriceOut": 39.72,
			// Возмещение 0 или 1, int
			"Reimburs": 0,
			// >>


			// << sale-in maybe, sale-out maybe
			// Остаток на конец периода, float
			"Balance": 8,
			// >>


			// << !!! reserved for internal use only
			// Хеш-сумма наименования [+], string
			"IDrugSHA": "2fd4e1c67a2d28fced849ee1bb76e7391b93eb12",
			// Связь Морион для наименования [+], object
			"IDrugLNK": "FIXME",
			// Хеш-сумма поставщика [+], string
			"ISuppSHA": "9e32295f8225803bb6d5fdfcc0674616a4413c1b",
			// Связь Морион для поставщика [+], object
			"ISuppLNK": "FIXME"
			// >>
		}]
	}]
}
```

[0]: https://github.com/pharmbase/rfc/blob/master/src/api-proc.md
