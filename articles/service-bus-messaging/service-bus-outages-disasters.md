---
title: "Изолирование приложений служебной шины Azure от простоев и аварий | Документация Майкрософт"
description: "В этой статье описаны методы, которые можно использовать для защиты приложений от возможного простоя служебной шины."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: tysonn
ms.assetid: fd9fa8ab-f4c4-43f7-974f-c876df1614d4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: bc84dbe5c26a834b2cff5f71ba5f541e94ba0b38
ms.lasthandoff: 04/13/2017


---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Рекомендации по изолированию приложений от простоев и аварий служебной шины
Критически важные приложения должны работать постоянно, даже при возникновении незапланированных простоев или аварий. В этой статье описаны методы, которые можно использовать для защиты приложений служебной шины от возможного простоя службы или аварии.

Простой определяется как временная недоступность служебной шины Azure. Простой может влиять на некоторые компоненты служебной шины, например на хранилище сообщений или даже на весь центр обработки данных. После устранения проблемы служебная шина снова становится доступной. Как правило, простой не приводит к утрате сообщений или других данных. Примером сбоя компонента является недоступность определенного хранилища сообщений. Примером простоя центра обработки данных является сбой питания центра обработки данных или неисправность сетевого коммутатора центра обработки данных. Простой может длиться от нескольких минут до нескольких дней.

Авария определяется как полная потеря единицы масштабирования или центра обработки данных служебной шины. Центр обработки данных может снова стать доступным, но может и не стать. Авария обычно вызывает потерю некоторых или всех сообщений или других данных. Примеры аварий — пожар, наводнение или землетрясение.

## <a name="current-architecture"></a>Текущая архитектура
Для хранения сообщений, отправляемых в очереди или разделы, служебная шина использует несколько хранилищ сообщений. Несекционированные очередь или раздел назначаются одному хранилищу сообщений. Если это хранилище сообщений недоступно, все операции с этой очередью или разделом завершатся с ошибкой.

Все сущности обмена сообщениями служебной шины (очереди, разделы, ретрансляторы) располагаются в пространстве имен службы, которое связывается с центром обработки данных. Служебная шина не поддерживает автоматическую георепликацию данных и не позволяет пространству имен охватывать несколько центров обработки данных.

## <a name="protecting-against-acs-outages"></a>Защита от простоев ACS
Если вы используете учетные данные ACS и ACS становится недоступной, клиенты больше не смогут получать маркеры. Клиенты, имеющие маркер на момент сбоя ACS, могут продолжать пользоваться служебной шиной, пока срок действия маркера не истечет. По умолчанию время существования маркера составляет 3 часа.

Для защиты от простоев ACS используйте маркеры подписи общего доступа (SAS). В этом случае клиент проходит проверку подлинности непосредственно у служебной шины, подписывая самостоятельно созданный маркер секретным ключом. Вызовы к ACS больше не требуются. Дополнительные сведения о токенах SAS см. в статье [Аутентификация и авторизация в служебной шине][Service Bus authentication].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Защита очередей и разделов от сбоев хранилища сообщений
Несекционированные очередь или раздел назначаются одному хранилищу сообщений. Если это хранилище сообщений недоступно, все операции с этой очередью или разделом завершатся с ошибкой. Секционированная очередь, с другой стороны, состоит из нескольких фрагментов. Все они хранятся в разных хранилищах сообщений. При отправке сообщения в секционированную очередь или раздел служебная шина назначает сообщение в один из фрагментов. В случае недоступности соответствующего хранилища сообщений служебная шина по возможности записывает сообщение в другой фрагмент. Дополнительные сведения о секционированных сущностях см. в статье [Секционированные очереди и разделы][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Защита от простоев или аварий центра обработки данных
Чтобы разрешить переключение между двумя центрами обработки данных, можно создать пространство имен службы служебной шины в каждом центре обработки данных. Например, предположим, что пространство имен службы служебной шины **contosoPrimary.servicebus.windows.net** находится в северо-центральной части США, а пространство имен **contosoSecondary.servicebus.windows.net** — в южно-центральной части США. Если сущность обмена сообщениями служебной шины должна оставаться доступной при простое центра обработки данных, эту сущность можно создать в обоих пространствах имен.

Дополнительные сведения см. в разделе "Сбой служебной шины в центре обработки данных Azure" в статье [Шаблоны асинхронного обмена сообщениями и высокий уровень доступности][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Защита конечных точек ретрансляции от простоев или аварий центра обработки данных
Георепликация конечных точек ретрансляции позволяет службе, предоставляющей конечную точку ретрансляции, оставаться доступной при простое служебной шины. Для достижения георепликации служба должна создать две конечные точки ретрансляции в разных пространствах имен. Пространства имен должны находиться в разных центрах обработки данных, а две конечные точки должны иметь разные имена. Например, первичная конечная точка может быть доступна как **contosoPrimary.servicebus.windows.net/myPrimaryService**, а ее вторичный аналог — как **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Затем служба прослушивает обе конечные точки, и клиент может вызывать службу через любую из них. Клиентское приложение случайным образом выбирает один из ретрансляторов в качестве первичной конечной точки и отправляет свой запрос активной конечной точке. Если операция завершается ошибкой с кодом ошибки, это означает, что конечная точка ретрансляции недоступна. Приложение открывает канал к резервной конечной точке и снова отправляет запрос. В этот момент активная и резервная конечные точки меняются ролями: клиентское приложение считает старую активную конечную точку новой резервной конечной точкой, а старую резервную конечную точку — новой активной конечной точкой. Если обе операции завершаются неудачно, роли двух сущностей не изменяются и возвращается сообщение об ошибке.

В примере [Георепликация с ретрансляцией сообщений служебной шины][Geo-replication with Service Bus relayed Messages] демонстрируется репликация ретрансляторов.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Защита очередей и разделов от простоев или аварий центра обработки данных
Для обеспечения устойчивости к простоям центра обработки данных при обмене сообщениями через брокер служебная шина поддерживает два подхода: *активную* и *пассивную* репликацию. В каждом из этих подходов, если заданная очередь или раздел должны оставаться доступными при простое центра обработки данных, то эти очередь или раздел можно создать в обоих пространствах имен. Обе сущности могут иметь одно и то же имя. Например, первичная очередь может быть доступна как **contosoPrimary.servicebus.windows.net/myQueue**, а ее вторичный аналог — как **contosoSecondary.servicebus.windows.net/myQueue**.

Если приложение не требует постоянной связи отправителя и получателя, в нем можно реализовать долгосрочную клиентскую очередь для предотвращения потери сообщений и защиты отправителя от любых временных ошибок служебной шины.

## <a name="active-replication"></a>Активная репликация
При активной репликации для каждой операции используются объекты в обоих пространствах имен. Любой клиент, отправляющий сообщение, отправляет две копии одного и того же сообщения. Первая копия отправляется основной сущности (например, **contosoPrimary.servicebus.windows.net/sales**), а вторая копия — дополнительной сущности (например, **contosoSecondary.servicebus.windows.net/sales**).

Клиент получает сообщения из обеих очередей. Получатель обрабатывает первую копию сообщения, а вторая копия пропускается. Во избежание дублирования сообщений отправитель должен помечать каждое сообщение уникальным идентификатором. Обе копии сообщения должны быть помечены одним и тем же идентификатором. Для указания идентификатора сообщения можно использовать свойства [BrokeredMessage.MessageId][BrokeredMessage.MessageId], [BrokeredMessage.Label][BrokeredMessage.Label] или пользовательские свойства. Получатель должен хранить список уже полученных им сообщений.

В примере [Георепликация с сообщениями служебной шины, отправляемыми через посредника][Geo-replication with Service Bus Brokered Messages] показана активная репликация сущностей обмена сообщениями.

> [!NOTE]
> При активной репликации количество операций удваивается, поэтому этот подход может привести к увеличению расходов.
> 
> 

## <a name="passive-replication"></a>Пассивная репликация
При отсутствии сбоев в пассивной репликации используется только одна из двух сущностей обмена сообщениями. Клиент отправляет сообщение активной сущности. При сбое операции на активной сущности с кодом ошибки, который указывает, что центр обработки данных, в котором размещена активная сущность, может быть недоступным, клиент отправляет копию сообщения резервной сущности. В этот момент активная и резервная сущности меняются ролями: клиент, отправляющий сообщение, считает старую активную сущность новой резервной сущностью, а старую резервную сущность — новой активной сущностью. Если обе операции завершаются неудачно, роли двух сущностей не изменяются и возвращается сообщение об ошибке.

Клиент получает сообщения из обеих очередей. Поскольку есть вероятность того, что получатель получит две копии одного сообщения, он должен устранить дублирование сообщений. Устранить дубликаты можно так же, как при активной репликации.

В целом пассивная репликация экономичнее активной, поскольку в большинстве случаев выполняется только одна операция. Задержки, пропускная способность и расходы для нее идентичны сценарию без репликации.

При использовании пассивной репликации сообщения могут быть утрачены или получены дважды в следующих сценариях:

* **Задержка или потеря сообщения.** Предположим, что отправитель успешно отправил сообщение m1 в основную очередь, а затем очередь стала недоступной до получения сообщения m1 получателем. Отправитель отправляет следующее сообщение m2 в дополнительную очередь. Если основная очередь временно недоступна, то получатель получит сообщение m1 после того, как очередь снова станет доступной. В случае сбоя получатель может никогда не получить сообщение m1.
* **Дублирование.** Предположим, что отправитель отправляет сообщение m в основную очередь. Служебная шина успешно обрабатывает m, но ей не удается отправить ответ. По истечении времени ожидания отправки сообщения отправитель посылает идентичную копию сообщения m в дополнительную очередь. Если получатель смог получить первую копию m до того как основная очередь стала недоступной, то он получит обе копии m приблизительно в одно и то же время. Если получатель не смог получить первую копию m до того, как основная очередь стала недоступной, то сначала он получит вторую копию m, но затем получит первую копию m, когда основная очередь станет доступна.

В примере [Георепликация с сообщениями служебной шины, отправляемыми через посредника][Geo-replication with Service Bus Brokered Messages] показана пассивная репликация сущностей обмена сообщениями.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об аварийном восстановлении см. в следующих статьях:

* [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure][Azure SQL Database Business Continuity]
* [Проектирование устойчивых приложений для Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[Geo-replication with Service Bus Relayed Messages]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

