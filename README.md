![Иллюстрация к проекту](https://github.com/hudoznik/hudoznik.runcode_redme/raw/master/Настройка_подключения.png)



## Чтобы записывать лог в базу 
### 1 - добавить подключение (Если для лога используется отдельная база)
```php
'error' = [
	'className' => '\\Bitrix\\Main\\DB\\MysqliConnection',
	'host' => 'localhost',
	'database' => 'databasename',
	'login' => 'user',
	'password' => 'password',
	'options' => 2.0
]
```

### 2 - В настройках модуля ввести имя подключения
Настройки\Настройки продукта\Настройки модулей.
Если подключения к базе небудет, то лог будет писаться в системный


**$return1c** - Переменная в которой хранится результат полученный из 1с.
**$resultBitrix** - Переменная в которой хранится результат выполнения в битрикс.

## Запись ошибок в алгоритме битрикса
```php
try {
	code ...
} catch (Throwable $th) {
	$error = $th;
	CodesFunc::$logDB->write($error, CodesFunc::$task);
}
```
## Запуск заданий через агента.
В параметр runAgent передается список заданий, к которрым привязан агент, через запятую в порядке выполнения.
Пример:
```bash
wget http://[**ВАШ_САЙТ**]/bitrix/components/hudoznik.runcode/runcode_run.php?runAgent=4,2 > /dev/null 2>&1
```
Запуск заданий без агента.
В параметр runIds передается список заданий, которые необходимо выполнить, через запятую в порядке выполнения.
Пример:
```bash
wget http://[**ВАШ_САЙТ**]/bitrix/components/hudoznik.runcode/runcode_run.php?runIds=4,2 > /dev/null 2>&1
```
Запуск заданий через консоль.
В параметрах скрипта передается список заданий, которые необходимо выполнить, через запятую в порядке выполнения.
Пример:
```bash
[**/ПУТЬ/ДО/САЙТА**]/bitrix/components/hudoznik.runcode/runcode_run.php?runIds=4,2  > /dev/null 2>&1
```

## Добавление времени выполнения в базу лога
```php
CodesFunc::timeRunAdd("key"); //key - должен быть уникальным, так как является ключем в массиве
CodesFunc::timeRunAdd("key", $value); //Добавление собственного значения
```
Зарезервированные параметры
get1c - получение из 1с
countRows1c - количество записей
execBitrix - выполнение в битриксе
send1c - отправка в 1с

## Вывод времени выполнения 
```php
$diff = sprintf( '%.6f sec.', microtime( true ) - $start );
echo "Время выполнения: $diff";
```

# Вспомогательные функции
## Возвращает ид записи в таблице по guid(Внешний код)
```php
CodesFunc::getId	ItemXML_ID(
	string|array $xml_id = '', // guid объекта из 1с
	string|array $where = '', // Условия отбора в формате sql
	$item = "rows", // имя объекта поиска (собственное)
	$table = "b_iblock_element" //Таблица в которой производится поиск
)
```
Пример 
```php
$id = CodesFunc::getIdItemXML_ID('2810cf6c-8450-11ee-98fb-d850e6d4b5be', 'IBLOCK_ID = 65', 'rows', "b_iblock_element");
//По нескольким столбцам
$id = CodesFunc::getIdItemXML_ID([$v->XML_ID, $v->NAME], ["XML_ID", "NAME"], 'IBLOCK_ID = 65', 'rows', "b_iblock_element");
```
## Прямая запись данных в таблицу
```php
CodesFunc::updates(
            $table, // Таблица в которой находятся записи
            $col_names, // Колонки таблицы с которыми проводится работа (только для INSERT)
            $data, // Записи которые надо обновить
            $col_names_update, // Колонки таблицы, которые надо обновить, если запись существует (только для UPDATE)
			$count // Необязательный. Порция в запросе для больших объемов (max_allowed_packet и bulk_insert_buffer_size)
        );
```
## Функция боработки пришедшего из 1с и примая запись в базу
```php
CodesFunc::replace_all_data_in_db_table(
	$table, //Таблица в которую идет запись
	$return1c, // Асоциативный массив для записи 
	$truncate, // Необязательный. true - очищает таблицу перед записью, по умолчанию false
)
