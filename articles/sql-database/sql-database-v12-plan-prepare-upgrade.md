---
title: "Планирование обновления до версии 12 базы данных SQL | Документация Майкрософт"
description: "Описание подготовительных мер и ограничений, которые следует учитывать при обновлении до версии 12 Базы данных SQL Azure."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 8020f904-ad27-40c5-8c59-bdf2e690f77d
ms.service: sql-database
ms.custom: V11
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: genemi
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 237aa26a40c34d8fb511881604cb26bf9380322e


---
# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Планирование и подготовка к обновлению базы данных SQL Azure до версии 12
В этой статье описано планирование обновления с версии 11 до 12 базы данных SQL Azure, а также необходимые подготовительные действия.

Доступен новый [портал Azure](https://portal.azure.com/) , поддерживающий обновление до версии 12.

В таблице ниже перечислены другие справочные статьи по версии 12.

| Заголовок и ссылка | Описание содержимого |
|:--- |:--- |
| [Возможности базы данных SQL Azure](sql-database-features.md) |Содержит матрицу возможностей базы данных SQL Azure и SQL Server. |
| [Создание базы данных в Базе данных SQL версии 12](sql-database-get-started.md) |Описание создания новой базы данных SQL Azure в версии 12. Перечислены различные варианты, кроме создания пустой базы данных. |

## <a name="plan-ahead"></a>Предварительное планирование
В приведенных ниже подразделах перечислены необходимые сведения, а также решения, которые необходимо принять, прежде чем начинать обновление своей базы данных SQL Azure до версии 12.

### <a name="version-clarification"></a>Уточнение версии
В этом документе описывается обновление Базы данных SQL Microsoft Azure с версии 11 до 12. С формальной точки зрения номера версий приближены к таким двум значениям, как указано в инструкции Transact-SQL **SELECT @@version;**:

* 12.0.2000.8 *(или немного более поздняя, версия 12)*;
* 11.0.9228.18 *(версия 11)*.
  * Иногда V11 также называли SAWA v2.

### <a name="service-tier-planning"></a>Планирование уровня служб
Начиная с этой версии 12, база данных SQL Azure поддерживает только уровни служб Basic, Standard и Premium. Уровни служб Web и Business не поддерживаются в версии 12. Таким образом, если вы планируете обновить свою базу данных SQL Azure с текущим уровнем служб Web или Business, необходимо решить, каким будет ее новый уровень служб.

Подробнее об уровнях служб Basic, Standard и Premium:

* [Уровни служб Базы данных SQL](sql-database-service-tiers.md)
* [Обновление баз данных SQL уровня Web или Business до новых уровней служб](sql-database-upgrade-server-portal.md)

### <a name="review-the-geo-replication-configuration"></a>Обзор конфигурации георепликации
Если ваша база данных SQL настроена для георепликации, то необходимо задокументировать ее текущую конфигурацию и остановить георепликацию, прежде чем приступить к подготовке обновления. После завершения обновления потребуется перенастроить базу данных для георепликации.

При этом важно оставить источник нетронутым и попрактиковаться на копии базы данных.

## <a name="preparation-actions"></a>Подготовительные действия
Завершив планирование, можно приступить к выполнению действий, описанных в последующих подразделах, для подготовки завершающего этапа обновления.

Подробное описание завершающего этапа обновления можно найти в справочных статьях, ссылки на которые приведены в начале этой статьи.

### <a name="service-tier-actions"></a>Действия для уровней служб
Ценовые категории служб уровня Web и Business не поддерживаются в версии 12.

Если уровень служб вашей базы данных SQL Azure 11 — Web или Business, в процессе обновления вам будет предложено активировать для нее поддерживаемый уровень. Вам будет предложен уровень, соответствующий показателям рабочей нагрузки вашей базы данных. Однако вы можете выбрать любой поддерживаемый уровень служб.

Можно уменьшить количество необходимых действий во время обновления, отключив для своей базы данных версии 11 уровень служб Web или Business до начала обновления. Это можно сделать на [портале Azure](https://portal.azure.com/).

Если вы не знаете, какой уровень служб выбрать, советуем начать с уровня Standard S2. Любой более низкий уровень предоставляет меньше ресурсов по сравнению с уровнями Web и Business.

### <a name="suspend-geo-replication-during-upgrade"></a>Приостановка георепликации во время обновления
Обновление до версии 12 невозможно, если для базы данных активирована георепликация. Необходимо сначала перенастроить базу данных, остановив использование георепликации.

После обновления можно будет снова активировать георепликацию для базы данных.

### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Открытие портов на виртуальной машине Azure для подключения клиентов
Если клиентская программа подключается к базы данных SQL V12, а клиент работает на виртуальной машине Azure (ВМ), необходимо открыть на ней следующие диапазоны портов:

* 11000–11999;
* 14000–14999.

Щелкните [здесь](sql-database-develop-direct-route-ports-adonet-v12.md) , чтобы узнать больше о портах для базы данных SQL V12. Эти порты необходимы для улучшения производительности базы данных SQL V12.

## <a name="a-idlimitationsalimitations-during-and-after-upgrade-to-v12"></a><a id="limitations"></a>Ограничения во время обновления до версии 12 и после него
### <a name="portals-for-v12"></a>Порталы для версии 12
Существует три портала для Azure с разными возможностями относительно Базы данных SQL версии 12.

* [http://portal.azure.com/](https://portal.azure.com/)<br/>Это новый портал Azure, все еще находящийся в состоянии предварительной версии. Этот портал пока не вполне достиг состояния общей доступности. На этом портале вы сможете сделать следующее:
  
  * управлять сервером и базой данных версии 12;
  * обновить базу данных версии 11 до версии 12.
* [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Со временем этот классический портал Azure может выйти из обращения. На этом портале вы сможете сделать следующее:
  
  * управлять сервером и базой данных версии 12;
  * обновить базу данных версии 11 до версии 12 *не удастся* .
* (http://*имя_сервера*.database.windows.net)<br/> Классический портал базы данных SQL Azure:
  
  * *не* позволяет управлять серверами версии 12.

Мы рекомендуем вам подключаться к своим базам данных SQL Azure с помощью Visual Studio 2015 (VS2015). VS2015 можно использовать для следующих задач:

* Выполнение инструкции Transact-SQL.
* Проектирование схемы.
* Разработка базы данных, работающей в сети или в автономном режиме.

Вместо этого можно скачать [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/) — бесплатную и полнофункциональную версию VS2015.

На странице базы данных на старом классическом портале Azure можно щелкнуть **Открыть в Visual Studio**, чтобы запустить VS2015 на компьютере для подключения к базе данных SQL Azure.

Кроме того, вы можете использовать SQL Server Management Studio (SSMS) 2014 с [накопительным пакетом обновления 6 (CU6)](http://support.microsoft.com/kb/3031047/) для подключения к базе данных SQL Azure. Дополнительные сведения приведены в этой записи блога:<br/>[Обновление клиентских средств для Базы данных SQL Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).

> [!IMPORTANT]
> Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="limitation-during-upgrade-to-v12"></a>Ограничения *во время* обновления до версии 12
База данных версии 11 продолжает обеспечивать доступ к данным во время обновления до версии 12. Тем не менее нужно учитывать еще пару ограничений.

| Ограничения  | Описание |
|:--- |:--- |
| Продолжительность обновления |Продолжительность обновления зависит от размера, выпуска и количества баз данных на сервере. Обновление может занять от нескольких часов до нескольких дней, особенно на серверах, содержащих базы данных<br/><br/>* размером более 50 ГБ или<br/>* находящиеся на уровне служб, отличном от "Премиум".<br/><br/>Создание новых баз данных на сервере во время обновления также может увеличить продолжительность обновления. |
| Отсутствие георепликации |Георепликация не поддерживается на сервере версии 12, если на нем выполняется обновление с версии 11. |
| База данных некоторое время недоступна на последнем этапе обновления до версии 12 |Во время обновления базы данных, принадлежащие серверу версии 11, остаются доступными. Однако подключение к серверу и базам данных ненадолго становится недоступным на последнем этапе, когда начинается переход от версии 11 к готовой версии 12.<br/><br/>Период перехода длится от 40 секунд до 5 минут. Для большинства серверов переход должен длиться до 90 секунд. Время перехода увеличивается для серверов с большим количеством баз данных или высокой рабочей нагрузкой записи в базы данных. |

### <a name="limitation-after-upgrade-to-v12"></a>Ограничения *после* обновления до версии 12
| Ограничения  | Описание |
|:--- |:--- |
| Невозможность восстановления версии 11 |Обновление на месте невозможно откатить или отменить. |
| Уровень служб Web или Business |После начала обновления сервер новой базы данных версии 12 уже не сможет распознать или принять уровень служб Web или Business. |

### <a name="export-and-import-after-upgrade-to-v12"></a>Экспорт и импорт *после* обновления до версии 12
Вы можете экспортировать или импортировать базу данных версии 12 с помощью [портала Azure](https://portal.azure.com/). Также вы можете выполнять экспорт или импорт с помощью любого из следующих инструментов:

* SQL Server Management Studio (SSMS);
* Visual Studio 2015
* Платформа Data-Tier Application Framework (DacFx)

Тем не менее, чтобы использовать эти инструменты, необходимо сначала установить их последние обновления, чтобы убедиться, что они поддерживают новые возможности версии 12.

* [Накопительный пакет обновления 6 для SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
* [Обновление для средств SQL Server Database в Visual Studio 2013 (февраль 2015 г.)](https://msdn.microsoft.com/data/hh297027)
* [Платформа Data-Tier Application Framework (DacFx) для Azure SQL Database версии 12 (февраль 2015 г.)](http://www.microsoft.com/download/details.aspx?id=45886)

> [!NOTE]
> Ссылки на предыдущие средства были обновлены 2 марта 2015 г. или позднее. Рекомендуется использовать эти обновленные средства.
> 
> 

### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Восстановление до версии 12 удаленной базы данных версии 11
В приведенном ниже сценарии описано восстановление удаленной базы данных SQL Azure 11 на сервере базы данных SQL Azure 12.

1. Предположим, у вас есть сервер базы данных SQL Azure 11. <br/>  На сервере расположена база данных с уровнем служб Web или Business.
2. Вы удаляете базу данных.
3. Вы обновляете сервер до версии 12.
4. Затем вы восстанавливаете базу данных на сервере. <br/>  Таким образом, база данных будет обновлена до версии 12 с уровнем служб Standard S0.
5. Вы можете активировать для базы данных другой поддерживаемый уровень служб, если S0 не подходит вам.

### <a name="powershell-cmdlets"></a>Командлеты PowerShell
Командлеты PowerShell доступны для запуска, остановки и мониторинга обновления Базы данных SQL Azure версии 11 или другой более ранней версии до версии 12.

* [Обновление до версии V12 Базы данных SQL с помощью PowerShell](sql-database-upgrade-server-powershell.md)

Справочную документацию по этим командлетам PowerShell см. в таких разделах:

* [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Командлет Stop- означает отмену, а не приостановку. После его выполнения возобновить обновление невозможно, можно только запустить его сначала. Командлет Stop- очищает и высвобождает все соответствующие ресурсы.

## <a name="failure-resolution"></a>Устранение ошибок
Если обновление завершится какой-либо ошибкой, ваша база данных версии 11 останется активной и доступной.

## <a name="related-links"></a>Связанные ссылки
*  [Возможности предварительной версии](https://azure.microsoft.com/services/preview/)

<!--Anchors-->
[Subheading 1]: #subheading-1



<!--HONumber=Dec16_HO1-->


