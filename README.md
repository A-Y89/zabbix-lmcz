# Шаблон мониторинга СПО «Локальный модуль «Честный ЗНАК» через http агент Zabbix.

## Обзор

Шаблон для мониторинга основных параметров ЛМЧЗ без внешних скриптов.
Данные собираются из API /api/v1/status 

## Требования

Версия Zabbix: 7.0 и выше.

## Протестированные версии

Шаблон протестирован на версии ЛМЧЗ:
1.4.0-421

## Конфигурация

> Дополнительных действий не требуется.

## Установка

1\. Создать хост для каждого ЛМЧЗ

2\. Присоединить шаблон к хосту

3\. Заполнить значения макросов {$LMCZ_USER} и {$LMCZ_PWD} в шаблоне, если одинаковые для всех хостов. Если каждый хост со своим логином и паролем - добавить соответствующие макросы к хосту и заполнить значениями.



### Макросы

|Имя|Описание|Значение по умолчанию|
|----|-----------|-------|
|{$LMCZ_USER}|Логин к ЛМЧЗ|<p>-</p>|
|{$LMCZ_PWD}|Пароль к ЛМЧЗ|<p>-</p>|

### Элементы данных

|Имя|Описание|Тип|Ключ и дополнительная информация|
|----|-----------------------|-------|----------------|
|Данные /api/v1/status|<p>Возвращает JSON массив с исходными данными.</p>|HTTP агент|lmcz.getStatus<br>GET http://{HOST.CONN}:5995/api/v1/status|
|Версия СПО «Локальный модуль «Честный ЗНАК»||Зависимый элемент данных|lmcz.version<p>**Предобработка**</p><ul><li><p>JSON: `$.version`</p></li></ul>|
|Версия базы данных «чёрного списка»||Зависимый элемент данных|lmcz.dbVersion<p>**Предобработка**</p><ul><li><p>JSON: `$.dbVersion`</p></li></ul>|
|Дата и время последней синхронизации||Зависимый элемент данных|lmcz.lastSync<p>**Предобработка**</p><ul><li><p>JSON: `$.lastSync`</p></li></ul>|
|ИНН участника оборота||Зависимый элемент данных|lmcz.inn<p>**Предобработка**</p><ul><li><p>JSON: `$.inn`</p></li></ul>|
|Идентификатор экземпляра СПО «Локальный модуль «Честный ЗНАК»||Зависимый элемент данных|lmcz.inst<p>**Предобработка**</p><ul><li><p>JSON: `$.inst`</p></li></ul>|
|Наименование программного обеспечения||Зависимый элемент данных|lmcz.name<p>**Предобработка**</p><ul><li><p>JSON: `$.name`</p></li></ul>|
|Последнее известное время запуска одной из действующих репликаций||Зависимый элемент данных|lmcz.lastUpdate<p>**Предобработка**</p><ul><li><p>JSON: `$.lastUpdate`</p></li></ul>|
|Признак, что Системе требуется загрузка БД||Зависимый элемент данных|lmcz.requiresDownload<p>**Предобработка**</p><ul><li><p>JSON: `$.requiresDownload`</p></li></ul>|
|Состояние репликации blocked_cis - localDocCount||Зависимый элемент данных|lmcz.replicationStatus.blocked_cis.localDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.blocked_cis.localDocCount`</p></li></ul>|
|Состояние репликации blocked_cis - serverDocCount||Зависимый элемент данных|lmcz.replicationStatus.blocked_cis.serverDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.blocked_cis.serverDocCount`</p></li></ul>|
|Состояние репликации blocked_gtin - localDocCount||Зависимый элемент данных|lmcz.replicationStatus.blocked_gtin.localDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.blocked_gtin.localDocCount`</p></li></ul>|
|Состояние репликации blocked_gtin - serverDocCount||Зависимый элемент данных|lmcz.replicationStatus.blocked_gtin.serverDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.blocked_gtin.serverDocCount`</p></li></ul>|
|Состояние репликации CIS - localDocCount||Зависимый элемент данных|lmcz.replicationStatus.cis.localDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.cis.localDocCount`</p></li></ul>|
|Состояние репликации CIS - serverDocCount||Зависимый элемент данных|lmcz.replicationStatus.cis.serverDocCount<p>**Предобработка**</p><ul><li><p>JSON: `$.replicationStatus.cis.serverDocCount`</p></li></ul>|
|Статус СПО «Локальный модуль «Честный ЗНАК»||Зависимый элемент данных|lmcz.status<p>**Предобработка**</p><ul><li><p>JSON: `$.status`</p></li></ul>|
|Тип режима обслуживания||Зависимый элемент данных|lmcz.operationMode<p>**Предобработка**</p><ul><li><p>JSON: `$.operationMode`</p></li></ul>|


### Триггеры

|Имя|Условия|Важность|Зависимости и дополнительная информация|
|--------------|---------------------------|--------|----------------|
|[ЛМЧЗ] Данные не синхронизировались более 1 суток|`last(/LMCZ by http agent/lmcz.lastSync)<(now()-86400)`|Предупреждение||
|[ЛМЧЗ] Некорректный статус ({ITEM.LASTVALUE})|`last(/LMCZ by http agent/lmcz.status)<>"ready"`|Высокая||
|[ЛМЧЗ] Некорректный тип режима обслуживания ({ITEM.LASTVALUE})|`last(/LMCZ by http agent/lmcz.operationMode)="service"`|Средняя||
|[ЛМЧЗ] Системе требуется загрузка БД|`last(/LMCZ by http agent/lmcz.requiresDownload)="true"`|Высокая||
