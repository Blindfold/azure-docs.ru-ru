---
title: "Создание службы Поиска Azure на портале Azure | Документация Майкрософт"
description: "Подготовка службы Поиска Azure на портале."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/05/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 379bc2e80a89b6d46db3bd536737583d51029328
ms.lasthandoff: 03/06/2017


---
# <a name="create-an-azure-search-service-in-the-portal"></a>Создание службы "Поиск Azure" на портале

В этой статье поясняется, как создать или подготовить службу Поиска Azure на портале. Инструкции для PowerShell см. в разделе [Управление службой поиска Azure с помощью PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Подписка (бесплатная или платная)

[Откройте бесплатную учетную запись Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) и используйте бесплатные кредиты, чтобы опробовать платные службы Azure. После того, как кредиты будут израсходованы, сохраните свою учетную запись. Вы сможете использовать ее для работы с бесплатными службами Azure, такими как веб-сайты. С вашей кредитной карты не будет взиматься плата, если вы явно не измените параметры и не попросите снимать плату.

Кроме того, вы можете [активировать преимущества подписчика MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Подписка MSDN каждый месяц приносит вам кредиты, которыми можно оплачивать использование платных служб Azure. 

## <a name="find-azure-search"></a>Как найти Поиск Azure
1. Войдите на [портал Azure](https://portal.azure.com/).
2. Щелкните знак "плюс" ("+") в верхнем левом углу.
3. Выберите **Интернет + мобильные устройства** > **Поиск Azure**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Присвоение имени службы и URL-адреса конечной точки

Имя службы является частью URL-адреса конечной точки, к которой отправляются вызовы API. Введите имя службы в поле **URL-адрес** . 

Требования к имени службы:
   * должно содержать от 2 до 60 знаков;
   * должно содержать только строчные буквы, цифры или дефисы ("-");
   * не может содержать дефис ("-") в первых двух знаках или в последнем знаке;
   * не может содержать последовательные дефисы ("--").

## <a name="select-a-subscription"></a>Выбор подписки
Если у вас несколько подписок, выберите ту, которая также содержит службы хранилища данных или файлов. Служба Поиска Azure может автоматически определить хранилище таблиц Azure и хранилище BLOB-объектов Azure, базу данных SQL и DocumentDB для индексирования посредством *индексаторов*, но только для служб в одной подписке.

## <a name="select-a-resource-group"></a>Выбор группы ресурсов
Группы ресурсов — это набор служб и ресурсов Azure, которые используются совместно. Например, если вы используете службу Поиска Azure для индексации базы данных SQL, то обе службы должны входить в одну группу ресурсов.

> [!TIP]
> При удалении группы ресурсов также удаляются службы внутри нее. Если проект прототипа использует нескольких служб, то, поместив их все в одну группу ресурсов, можно упростить очистку после завершения этого проекта. 

## <a name="select-a-hosting-location"></a>Выбор расположения размещения 
Являясь службой Azure, Поиск Azure может размещаться в центрах обработки данных по всему миру. Обратите внимание, что [цены могут отличаться](https://azure.microsoft.com/pricing/details/search/) в зависимости от географического региона.

## <a name="select-a-pricing-tier-sku"></a>Выбор ценовой категории (номера SKU)
[Служба поиска Azure сейчас предлагается в нескольких ценовых категориях](https://azure.microsoft.com/pricing/details/search/): "Бесплатный", "Базовый" и "Стандартный". Каждая категория отличается собственным [объемом и ограничениями](search-limits-quotas-capacity.md). Подробные сведения см. в статье [Выбор SKU или ценовой категории для службы поиска Azure](search-sku-tier.md).

В данном пошаговом руководстве мы выбрали для службы уровень "Стандартный".

## <a name="create-your-service"></a>Создание службы

Не забудьте закрепить службу на панели мониторинга, чтобы иметь к ней быстрый доступ после каждого входа в систему.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Выполните масштабирование службы
Создание службы может занять несколько минут (от&15; минут, в зависимости от уровня). Подготовив службу, вы можете выполнить ее масштабирование в соответствии со своими потребностями. Выбрав уровень "Стандартный" для службы поиска Azure, вы сможете масштабировать свою службу в двух измерениях: репликах и секциях. Если вы выбрали уровень "Базовый", то сможете добавлять только реплики. Если подготовлена бесплатная служба, масштабирование будет недоступно.

***Секции*** позволяют службе хранить данные и осуществлять поиск в большем количестве документов.

***Реплики*** дают службе возможность справляться с повышенной нагрузкой запросов поиска.

> [!Important]
> У службы должно быть [2 реплики для выполнения соглашения об уровне обслуживания только для чтения и 3 реплики для выполнения соглашения об уровне обслуживания чтения и записи](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Перейдите в колонку своей службы поиска на портале Azure.
2. В области навигации слева щелкните **Параметры** > **Масштаб**.
3. Используйте ползунок, чтобы добавить реплики или секции.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Каждый уровень имеет свои [ограничения](search-limits-quotas-capacity.md) на общее количество единиц поиска, разрешенных для одной службы (реплики * секции = общее количество единиц поиска).

## <a name="when-to-add-a-second-service"></a>Когда следует добавлять вторую службу

Подавляющее большинство пользователей использует только одну службу, подготовленную на уровне, который обеспечивает [правильный баланс ресурсов](search-sku-tier.md). В одной службе может размещаться несколько индексов с учетом [максимального ограничения выбранного уровня](search-capacity-planning.md), при этом все индексы изолированы друг от друга. В Поиске Azure запросы могут направляться только в один индекс, сводя к минимуму вероятность случайного или преднамеренного извлечения данных из других индексов в той же службе.

Несмотря на то, что большинство пользователей использует только одну службу, избыточность служб может потребоваться, если в эксплуатационных целях необходимо обеспечить следующее:

+ Аварийное восстановление (сбой центра обработки данных). Поиск Azure не обеспечивает немедленную отработку отказа в случае сбоя. Рекомендации и инструкции см. в статье [Администрирование службы поиска Azure на портале Azure](search-manage.md).
+ Изучая мультитенантную модель, мы определили, что использование дополнительных служб обеспечивает оптимальную архитектуру. Дополнительные сведения см. в статье [Шаблоны разработки для мультитенантных приложений SaaS и Поиска Azure](search-modeling-multitenant-saas-applications.md).
+ Для глобально развернутых приложений может потребоваться наличие экземпляра Поиска Azure в нескольких регионах, чтобы минимизировать задержку международного трафика приложения.

> [!NOTE]
> В Поиске Azure невозможно разделить индексирование и рабочие нагрузки запросов. Поэтому вы не сможете создать несколько служб для разделенных рабочих нагрузок. Индекс всегда запрашивается в службе, в которой он был создан (невозможно создать индекс в одной службе и скопировать его на другую).
>

Для обеспечения высокого уровня доступности вторая служба не является обязательной. Высокая доступность для запросов достигается за счет использования двух или более реплик в одной службе. Обновление реплик выполняется последовательно, то есть при развертывании обновления службы должна работать по крайней мере одна реплика. Дополнительные сведения об обеспечении бесперебойной работы см. в [Соглашении об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Дальнейшие действия
Подготовив службу Поиска Azure, вы можете [определить индекс](search-what-is-an-index.md), чтобы передавать свои данные и осуществлять в них поиск.

Для доступа к службе из кода или сценария укажите URL-адрес (*имя_службы*.search.windows.net) и ключ. Ключи администратора предоставляют полный доступ. Ключи запроса предоставляют доступ только для чтения. Чтобы приступить к работе, ознакомьтесь с разделом [Использование службы поиска Azure в приложении .NET](search-howto-dotnet-sdk.md).

В разделе [Начало работы со службой поиска Azure на портале](search-get-started-portal.md) представлено краткое руководство по работе с порталом.


