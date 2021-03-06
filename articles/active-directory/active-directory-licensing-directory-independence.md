---
title: "Характеристики каталогов Azure Active Directory | Документация Майкрософт"
description: "Управление каталогами Azure Active Directory как полностью независимыми ресурсами."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 5ec00d5e8380f121dd9302cf08a0708c530aab9b
ms.contentlocale: ru-ru
ms.lasthandoff: 03/01/2017


---
# <a name="understand-how-multiple-azure-active-directory-directories-interact"></a>Сведения о взаимодействии нескольких каталогов Azure Active Directory
В системе Azure Active Directory (Azure AD) каждый каталог является полностью независимым ресурсом: равноправным, полнофункциональным и логически независимым от других каталогов, которыми вы управляете. Между каталогами нет связи типа «родительский объект — дочерний объект». Такая независимость каталогов подразумевает и независимость ресурсов, административную независимость и независимость синхронизации.

## <a name="resource-independence"></a>Независимость ресурсов
Если создать или удалить ресурс в одном каталоге, это не повлияет на какой-либо ресурс в другом каталоге, за исключением внешних пользователей, что описано ниже. Если вы используете настраиваемый домен contoso.com с одним каталогом, его невозможно использовать с любым другим каталогом.

## <a name="administrative-independence"></a>Административная независимость
Если пользователь без прав администратора каталога Contoso создает тестовый каталог Test, то:

* По умолчанию пользователь, создающий новый каталог, добавляется в него в качестве внешнего пользователя, и ему назначается роль глобального администратора этого каталога.
* У администраторов каталога Contoso нет прямых прав администратора для каталога Test, пока администратор Test явно не предоставит им эти привилегии. Администраторы Contoso могут контролировать доступ к каталогу Test, если они управляют учетной записью пользователя, создавшего каталог Test.
* Если изменить (добавить или удалить) роль администратора для пользователя в одном каталоге, это изменение не повлияет на какую-либо роль администратора пользователя в другом каталоге.

## <a name="synchronization-independence"></a>Независимость синхронизации
Вы можете настроить каждый каталог Azure AD независимо, чтобы синхронизировать данные из одного из следующих экземпляров:

* инструмент синхронизации каталогов (DirSync) для синхронизации данных с одним лесом AD;
* соединитель Azure Active Directory для Forefront Identity Manager для синхронизации данных с одним или несколькими локальными лесами либо источниками данных за пределами Azure AD.

## <a name="add-an-azure-ad-directory"></a>Добавление каталога Azure AD
Чтобы добавить каталог Azure AD на классический портал Azure, выберите расширение Azure Active Directory слева и нажмите кнопку **Добавить**.

> [!NOTE]
> В отличие от других ресурсов Azure каталоги не являются дочерними ресурсами подписки Azure. Поэтому, если отменить подписку Azure или дождаться истечения срока ее действия, вы по-прежнему сможете работать с данными каталога с помощью Azure PowerShell, API Azure Graph или других интерфейсов, таких как Центр администрирования Office 365. Можно также связать другую подписку с каталогом.
>
>

## <a name="next-steps"></a>Дальнейшие действия
Общие сведения о лицензировании Azure AD и рекомендации по работе с этой службой см. в статье [Что такое лицензирование Azure Active Directory?](active-directory-licensing-what-is.md)

