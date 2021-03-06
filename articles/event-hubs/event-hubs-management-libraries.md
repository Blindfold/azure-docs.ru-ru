---
title: "Библиотеки управления концентраторов событий Azure | Документация Майкрософт"
description: "Управление пространствами имен и сущностями концентраторов событий из .NET"
services: event-hubs
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 4/10/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a9023448c4ced1edf54c84bb103454cbd76fbfba
ms.lasthandoff: 04/12/2017


---

# <a name="event-hubs-management-libraries"></a>Библиотеки управления концентраторов событий

Библиотеки управления концентраторов событий могут динамически подготавливать пространства имен и сущности концентраторов событий. Это дает возможность реализовывать сложные развертывания и сценарии обмена сообщениями, позволяя программно определять, какие сущности следует подготовить. В настоящее время эти библиотеки доступны для .NET.

## <a name="supported-functionality"></a>Поддерживаемые функции

* Создание, обновление, удаление пространства имен.
* Создание, обновление, удаление концентраторов событий.
* Создание, обновление, удаление группы потребителей.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе с библиотеками управления концентраторов событий, нужно пройти аутентификацию в Azure Active Directory (AAD). AAD требует аутентификации в качестве субъекта-службы, предоставляющего доступ к вашим ресурсам Azure. Сведения о создании субъекта-службы см. в одной из приведенных ниже статей:  

* [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Использование интерфейса командной строки Azure для создания субъекта-службы и доступа к ресурсам](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

В этих руководствах вы получите `AppId` (идентификатор клиента), `TenantId` и `ClientSecret` (ключ аутентификации), которые используются библиотеками управления для аутентификации. Необходимо иметь разрешения роли "Владелец" для группы ресурсов, которую вы хотите использовать.

## <a name="programming-pattern"></a>Шаблон программирования

Шаблон обработки любого ресурса концентраторов событий придерживается общего протокола.

1. Получение маркера из Azure Active Directory с помощью библиотеки `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Создание объекта `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Присвоение параметрам `CreateOrUpdate` указанных значений.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Выполнение вызова.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Пример управления для .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Справочник по Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

