---
title: "Обновление REST API службы поиска Azure до версии 2016-09-01 | Документация Майкрософт"
description: "Обновление REST API службы поиска Azure до версии 2016-09-01"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f
ms.lasthandoff: 01/14/2017

---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Обновление REST API службы поиска Azure до версии 2016-09-01
Если вы используете предварительную версию 2015-02-28 или 2015-02-28-Preview [REST API службы поиска Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) или более раннюю, эта статья поможет вам обновить приложения для использования следующей общедоступной версии API 2016-09-01.

Версия 2016-09-01 REST API содержит некоторые изменения из более ранних версий. Эти отличия в основном обратно совместимы, поэтому изменение кода потребует минимальных усилий в зависимости от версии, которая использовался ранее. В разделе [Действия по обновлению](#UpgradeSteps) вы найдете инструкции о том, как изменить код для использования новой версии API.

> [!NOTE]
> Экземпляр службы поиска Azure поддерживает несколько версий REST API, включая последнюю. Можно продолжать использовать версию, которая больше не является последней, но рекомендуется выполнить перенос кода, чтобы использовать последнюю версию.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Новые возможности в версии 2016-09-01
Версия 2016-09-01 — это второй общедоступный выпуск REST API службы поиска Azure. Новые возможности в этой версии API:

* [Пользовательские анализаторы](https://aka.ms/customanalyzers) позволяют получить контроль над процессом преобразования текста в индексируемые и поддерживающие поиск токены.
* Индексаторы [хранилища BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md) и [хранилища таблиц Azure](search-howto-indexing-azure-tables.md) позволяют легко импортировать данные из хранилища Azure в службу поиска Azure по расписанию или по требованию.
* [Сопоставления полей](search-indexer-field-mappings.md) позволяют настроить, каким образом индексаторы будут импортировать данные в службу поиска Azure.
* Теги eTag позволяют обновлять определения индексов, индексаторов и источников данных безопасно в отношении параллельного выполнения. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению
При обновлении с версии 2015-02-28 вам, вероятно, понадобится изменить в коде только номер версии. Изменение кода может потребоваться только в следующих случаях:

* Код завершается ошибкой, если в ответе API возвращаются нераспознанные свойства. По умолчанию приложение должно игнорировать свойства, которые не распознаются.
* Код сохраняет запросы API и пытается повторно отправить их в новую версию API. Например, это может произойти, если приложение сохраняет маркеры продолжения, возвращенные API поиска (см. сведения об `@search.nextPageParameters` в [справочнике по API поиска](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1)).

Если одна из этих ситуаций относится к вам, может потребоваться изменить код соответствующим образом. В противном случае вам понадобится вносить изменения, только если вы хотите использовать [новые возможности](#WhatsNew) версии 2016-09-01.

Тоже самое относится и к обновлению с версии 2015-02-28-Preview. Однако следует учитывать, что некоторые возможности предварительной версии недоступны в версии 2016-09-01:

* Поддержка индексатора хранилища BLOB-объектов Azure для CSV-файлов и больших двоичных объектов, содержащих массивы JSON.
* синонимы;
* Запросы "Скорее всего".

Если код использует эти возможности, вы не сможете выполнить обновление до версии 2016-09-01, если не отключите их.

> [!IMPORTANT]
> Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
> 
> 

## <a name="conclusion"></a>Заключение
Дополнительные сведения об использовании REST API службы поиска Azure см. в [справочнике по API](https://msdn.microsoft.com/library/azure/dn798935.aspx) на сайте MSDN.

Будем рады вашим отзывам о службе поиска Azure. Если вы столкнулись с проблемами, то всегда можете обратиться за помощью на [форуме по службе поиску Azure на сайте MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) или [StackOverflow](http://stackoverflow.com/). Если вы задаете вопрос о службе поиска Azure на сайте StackOverflow, обязательно добавьте к нему `azure-search`.

Благодарим вас за использование поиска Azure!


