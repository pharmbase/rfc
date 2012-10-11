# Формат JSON Cron (Draft) #

Формат для передачи параметров [Cron](http://en.wikipedia.org/wiki/Cron)

* Формат [JSON](http://json.org/json-ru.html)
* Кодировка [UTF-8](http://ru.wikipedia.org/wiki/UTF-8)
* `/* */` - это комментарий только для пояснения и он должен отсутствовать в реальных данных

Спецификация-пример (1 запись):

```json
[
	{
		/* min (0 - 59), string */
		"Min": "0",
		/* hour (0 - 23), string */
		"Hour": "0",
		/* day of month (1 - 31), string */
		"DayOfMonth": "*",
		/* month (1 - 12), string */
		"Month": "*",
		/* day of week (0 - 6) (0 is Sunday, or use names), string */
		"DayOfWeek": "*",
		/* command to be executed */
		"Command": "m.exe --sale-out/daily"
	}
]
```
