---
title: "Соглашения для обмена данными в сценариях B2B с использованием Azure Logic Apps | Документация Майкрософт"
description: "Создание соглашений, благодаря которым партнеры могут взаимодействовать, реализуя сценарии B2B с помощью Azure Logic Apps и пакета интеграции Enterprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 8dd90542440322e6d57406cd950fa0a13bd4fe1d
ms.openlocfilehash: 1068b5bd5f2c86de0c82f5a96cb2718645c0a3d3
ms.contentlocale: ru-ru
ms.lasthandoff: 02/07/2017


---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Партнерские соглашения для обмена данными в сценариях B2B с использованием Azure Logic Apps и пакета интеграции Enterprise

Соглашения являются основой взаимодействий "бизнес — бизнес" (B2B), так как они позволяют бизнес-сущностям легко обмениваться данными с помощью стандартных протоколов. В контексте реализации сценариев В2В с помощью интеграции Enterprise соглашение — это договоренность о взаимодействии "бизнес — бизнес" между торговыми партнерами. Соглашение основывается на взаимодействии, которое устанавливается между двумя партнерами с учетом стандартов транспорта или протокола.

Интеграция Enterprise поддерживает три стандарта протокола или транспорта:

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Для чего используются соглашения

Ниже перечислены некоторые преимущества, связанные с использованием соглашений.

* Возможность для разных организаций и компаний обмениваться данными в привычном формате.
* Повышает эффективность при проведении транзакций "бизнес-бизнес".
* Быстрое создание, использование и администрирование приложений интеграции Enterprise.

## <a name="how-to-create-agreements"></a>Как создать соглашение

* [Создание соглашения AS2](logic-apps-enterprise-integration-as2.md)
* [Создание соглашения X12](logic-apps-enterprise-integration-x12.md)
* [Создание соглашения EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Как использовать соглашение

Вы можете создавать [приложения логики](logic-apps-what-are-logic-apps.md "Дополнительные сведения о приложениях логики") с возможностями B2B, используя созданные вами соглашения.

## <a name="how-to-edit-an-agreement"></a>Как изменить соглашение

Любое соглашение можно изменить следующим образом.

1. Выберите учетную запись интеграции, содержащую изменяемое соглашение.

2. Щелкните плитку **Соглашения**.

3. В колонке **Соглашения** выберите соглашение.

4. Нажмите кнопку **Изменить**. Внесите нужные изменения.

5. Нажмите кнопку **ОК**, чтобы сохранить изменения.

## <a name="how-to-delete-an-agreement"></a>Как удалить учетную запись

Любое соглашение можно удалить. Для этого сделайте следующее.

1. Выберите учетную запись интеграции, содержащую удаляемое соглашение.
2. Щелкните плитку **Соглашения**.
3. В колонке **Соглашения** выберите соглашение.
4. Щелкните **Удалить**.
5. Подтвердите удаление соглашения.

    В колонке соглашений удаленное соглашение больше не будет отображаться.

## <a name="next-steps"></a>Дальнейшие действия
* [Создание соглашения AS2](logic-apps-enterprise-integration-as2.md)

