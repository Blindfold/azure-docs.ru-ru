---
title: "Формирование строк фильтра для конструктора таблиц | Документация Майкрософт"
description: "Построение строк фильтра для конструктора таблиц"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98b06b14ca7897cce884f6d80f998661cacb7ea4


---
# <a name="constructing-filter-strings-for-the-table-designer"></a>Построение строк фильтра для конструктора таблиц
## <a name="overview"></a>Обзор
Для фильтрации данных в таблице Azure, отображаемой в **конструкторе таблиц**Visual Studio, нужно создать строку фильтра и ввести ее в поле фильтра. Синтаксис строки фильтра определяется службами данных WCF и напоминает предложение SQL WHERE, но отправляется в службу таблиц через HTTP-запрос. **Конструктор таблиц** обрабатывает соответствующую кодировку, поэтому для фильтрации по определенному значению свойства в поле фильтра необходимо ввести только имя свойства, оператор сравнения, значение критерия и, при необходимости, логический оператор. Не нужно включать параметр запроса $filter, как при построении URL-адреса для запроса к таблице через [Справочник по API REST служб хранилища](http://go.microsoft.com/fwlink/p/?LinkId=400447).

Службы данных WCF основаны на протоколе [OData](http://go.microsoft.com/fwlink/p/?LinkId=214805). Дополнительные сведения о параметре системного запроса фильтрации (**$filter**) см. в [спецификации соглашений об универсальных кодах ресурсов (URI) для OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Операторы сравнения
Для всех типов свойств поддерживаются следующие логические операторы:

| Логический оператор | Описание | Пример строки фильтра |
| --- | --- | --- |
| eq |Равно |City eq 'Redmond' |
| gt |Больше |Price gt 20 |
| ge |Больше или равно |Price ge 10 |
| lt |Меньше |Price lt 20 |
| le |Меньше или равно |Price le 100 |
| ne |Не равно |City ne 'London' |
| и |и |Price le 200 and Price gt 3.5 |
| или |или |Price le 3.5 or Price gt 200 |
| not |not |not isAvailable |

При создании строки фильтра соблюдайте следующие правила.

* Для сравнения свойства со значением используйте логические операторы. Обратите внимание, что нельзя сравнивать свойство и динамическое значение. Одна из частей выражения должна быть константой.
* Во всех частях строки фильтра учитывается регистр.
* Для получения допустимых результатов фильтра значения константы и свойства должны иметь одинаковый тип данных. Дополнительные сведения о поддерживаемых типах свойств см. в статье [Общие сведения о модели данных службы таблиц](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Фильтрация по строковым свойствам
Во время фильтрации по строковым свойствам заключайте строковую константу в одинарные кавычки.

В следующем примере выполняется фильтрация по свойствам **PartitionKey** и **RowKey**. Дополнительные неключевые свойства также можно добавить в строку фильтра.

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Каждое выражение фильтра можно заключить в круглые скобки, хотя это не обязательно:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Обратите внимание, что служба таблиц не поддерживает запросы с подстановочными знаками, они также не поддерживаются в конструкторе таблиц. Тем не менее можно выполнять фильтрацию по префиксу, используя операторы сравнения и нужный префикс. Следующий пример возвращает сущности, в которых свойство LastName начинается с буквы "A":

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Фильтрация по числовым свойствам
Чтобы отфильтровать целое число или число с плавающей запятой, указывайте число без кавычек.

Следующий пример возвращает все сущности свойства Age, значения которых больше 30.

    Age gt 30

Следующий пример возвращает все сущности свойства AmountDue, значения которых меньше или равны 100,25.

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Фильтрация по логическим свойствам
Для фильтрации по логическим значениям указывайте **true** или **false** без кавычек.

В следующем примере возвращаются все сущности, в которых свойство IsActive имеет значение **true**:

    IsActive eq true

Это выражение фильтрации также можно записать без логического оператора. В следующем примере служба таблиц также вернет все сущности, в которых свойство IsActive имеет значение **true**.

    IsActive

Вернуть все сущности, в которых свойство IsActive имеет значение false, можно с помощью оператора not.

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Фильтрация по свойствам DateTime
Для фильтрации по значению DateTime укажите ключевое слово **datetime** , за которым введите константу даты и времени в одинарных кавычках. Константа даты и времени должна быть указана в комбинированном формате UTC, как описано в статье [Форматирование значений свойства DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Следующий пример возвращает сущности, в которых свойство CustomerSince имеет значение 10 июля 2008 г.

    CustomerSince eq datetime'2008-07-10T00:00:00Z'



<!--HONumber=Nov16_HO3-->


