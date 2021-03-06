---
title: "Рекомендации по настройке производительности запросов Базы данных SQL Azure | Документация Майкрософт"
description: "Помощник по работе с базами данных SQL Azure предоставляет рекомендации для существующих баз данных SQL, которые могут повысить производительность текущих запросов."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: a8d0b08abc7e3c688f9ab79499b3459b33f06848
ms.lasthandoff: 02/02/2017


---
# <a name="sql-database-advisor"></a>Помощник по работе с базами данных SQL

База данных SQL Azure изучает работу приложения, адаптируется к нему и предоставляет настраиваемые рекомендации, что позволяет добиться максимальной производительности баз данных SQL. Помощник по базам данных SQL предоставляет рекомендации по созданию и удалению индексов, параметризации запросов и устранению ошибок схемы. Помощник оценивает производительность, анализируя журнал использования базы данных SQL. После этого он предоставляет рекомендации, наиболее подходящие для типовых нагрузок в вашей базе данных. 

Рекомендации для серверов базы данных SQL Azure указаны ниже. Сейчас можно настроить автоматическое применение рекомендаций по созданию и удалению индексов. Дополнительные сведения см. в разделе [Включение автоматического управления индексами](sql-database-advisor-portal.md#enable-automatic-index-management).

## <a name="create-index-recommendations"></a>Рекомендации по созданию индексов
**созданию индексов** появляются, когда служба базы данных SQL обнаруживает отсутствующий индекс, создание которого приведет к повышению производительности рабочих нагрузок в ваших базах данных (только некластеризованные индексы).

## <a name="drop-index-recommendations"></a>Рекомендации по удалению индексов
**удалению индексов** появляются, когда служба базы данных SQL обнаруживает повторяющиеся индексы (в настоящее время доступны только в предварительной версии и применяются только для повторяющихся индексов).

## <a name="parameterize-queries-recommendations"></a>Рекомендации по параметризации запросов
Рекомендации по **параметризации запросов** появляются при наличии одного или нескольких запросов, которые постоянно перекомпилируются, но в результате для них создается одинаковый план выполнения запроса. Это условие дает возможность применить принудительную параметризацию, которая позволит кэшировать планы запросов и повторно использовать их в будущем, повышая производительность и снижая использование ресурсов. 

Каждый запрос, отправленный в SQL Server, сначала должен быть скомпилирован для формирования плана выполнения. Каждый создаваемый план добавляется в кэш планов и может быть использован повторно при последующем выполнении этого запроса, устраняя необходимость в дополнительной компиляции. 

Приложения, отправляющие запросы, которые содержат непараметризованные значения, могут привести к снижению производительности, так как для каждого такого запроса с различными значениями параметров план выполнения компилируется заново. Во многих случаях для одинаковых запросов с отличными значениями параметров создаются одинаковые планы выполнения, но эти планы по-прежнему добавляются в кэш планов по отдельности. Планы выполнения повторных операций компиляции используют ресурсы базы данных, увеличивают длительность запроса и переполняют кэш планов, что приводит к исключению планов из него. Такое поведение SQL Server можно изменить, задав параметр принудительной параметризации для базы данных. 

Чтобы было проще оценить влияние этой рекомендации, вам будет предоставлено сравнение фактического и предполагаемого потребления ресурсов ЦП (после применения рекомендации). Помимо экономии ресурсов ЦП, снизится длительность обработки запроса за счет времени, затрачиваемого на компиляцию. Кроме того, уменьшится дополнительная нагрузка на кэш планов, что позволит хранить в кэше большинство планов и повторно их использовать. Вы можете быстро и легко применить эту рекомендацию, щелкнув команду **Применить**. 

Через несколько минут после применения этой рекомендации для вашей базы будет принудительно включена параметризация и запущен мониторинг, который длится примерно 24 часа. По истечении этого периода вы сможете просмотреть отчет о проверке, содержащий информацию об использовании ресурсов ЦП для базы данных за 24 часа до и через 24 часа после применения рекомендации. Помощник по базам данных SQL имеет механизм безопасности, который автоматически отменит примененную рекомендацию в случае, если будет обнаружено снижение производительности.

## <a name="fix-schema-issues-recommendations"></a>Рекомендации по устранению ошибок схемы
Рекомендации по **устранению ошибок схемы** появляются, когда служба базы данных SQL обнаруживает аномальное количество относящихся к схеме ошибок SQL, которые возникают в вашей базе данных SQL Azure. Эта рекомендация обычно появляется, когда в базе данных возникает несколько связанных со схемой ошибок (недопустимое имя столбца, недопустимое имя объекта и т. д.) в течение часа.

"Ошибки схемы" относятся к классу синтаксических ошибок в SQL Server, которые происходят, когда определение запроса SQL и определение схемы базы данных не согласованы. Например, один из столбцов, ожидаемых запросом, может отсутствовать в целевой таблице или наоборот. 

Рекомендация по устранению ошибок схемы появляется, когда служба базы данных SQL обнаруживает аномальное количество относящихся к схеме ошибок SQL, которые возникают в базе данных SQL Azure. В следующей таблице показаны ошибки, которые относятся к схеме.

| Код ошибки SQL | Сообщение |
| --- | --- |
| 201 |Процедура или функция "*" ожидает параметр "*", который не был указан. |
| 207 |Недопустимое имя столбца "*". |
| 208 |Недопустимое имя объекта "*". |
| 213 |Имя столбца или число предоставленных значений не соответствует определению таблицы. |
| 2812 |Не удалось найти хранимую процедуру "*". |
| 8144 |Для процедуры или функции * указано слишком много аргументов. |

## <a name="next-steps"></a>Дальнейшие действия
Отслеживайте рекомендации и продолжайте применять их для повышения производительности. Рабочие нагрузки базы данных являются динамическими и меняются непрерывно. Помощник по работе с базами данных SQL продолжает отслеживать работу и давать рекомендации, которые могут повысить производительность базы данных. 

* Ознакомьтесь с разделом [Помощник по работе с базами данных SQL](sql-database-advisor-portal.md) , чтобы узнать, как использовать помощник по базам данных SQL на портале Azure.
* См. статью [Анализ производительности запросов базы данных Azure SQL](sql-database-query-performance.md), чтобы узнать о том, как просмотреть влияние на производительность основных запросов.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Хранилище запросов](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Контроль доступа на основе ролей](../active-directory/role-based-access-control-configure.md)


