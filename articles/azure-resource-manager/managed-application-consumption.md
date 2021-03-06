---
title: "Использование управляемого приложения Azure | Документация Майкрософт"
description: "В этой статье описывается, как создавать управляемое приложение Azure с помощью опубликованных файлов."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2763da60fe25f2ca55603ecfcbbcefe3e368c25d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="consume-an-azure-managed-application"></a>Использование управляемого приложения Azure

Как описано в статье [Обзор управляемых приложений Azure](managed-application-overview.md), существует два сценария работы с управляемыми приложениями: первый — для издателя или независимого поставщика программного обеспечения, желающего создать управляемое приложение и предоставить его пользователям, и второй — для пользователя, использующего управляемое приложение. В этой статье рассматривается второй сценарий и объясняется, как конечный пользователь может использовать управляемое приложение, предоставленное независимым поставщиком программного обеспечения.

В настоящее время использовать управляемое приложение можно с помощью интерфейса командной строки Azure или портала Azure. 

## <a name="create-the-managed-application-using-cli"></a>Создание управляемого приложения с помощью интерфейса командной строки 

Необходимо получить идентификатор определения устройства, которые вы хотите использовать.

Создать управляемое приложение с использованием интерфейса командной строки Azure можно двумя способами. Это можно сделать с помощью обычной команды развертывания шаблона или с помощью новой команды, предназначенной специально для этой цели.

### <a name="create-using-template-deployment-command"></a>Создание с помощью команды развертывания шаблона

Разверните файл applianceMainTemplate.json, который создал поставщик.

Создайте две группы ресурсов: одну для создания ресурсов устройства (Microsoft.Solutions/appliances), а вторую — для содержания всех ресурсов, определенных в файле mainTemplate.json. Эта группа ресурсов управляется независимым поставщиком программного обеспечения.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Используйте `westcentralus` в качестве расположения группы ресурсов.
>


Затем используйте следующую команду, чтобы развернуть файл applianceMainTemplate.json в mainResourceGroup.

```azurecli
az group deployment --name managedAppDeployment --resourceGroup mainResourceGroup 
      --templateUri  
```

При выполнении предыдущего шаблона появится запрос на ввод значений параметров, определенных в шаблоне. Помимо параметров, необходимых для подготовки ресурсов в шаблоне, понадобятся два значения ключевых параметров:

- Свойство managedResourceGroupId — это идентификатор группы ресурсов, где создаются ресурсы, определенные в файле applianceMainTemplate.json. Он имеет формат `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. В предыдущем примере это идентификатор `managedResourceGroup`.
- Свойство applianceDefinitionId — это идентификатор определения ресурсов управляемого приложения. Данное значение предоставляет независимый поставщик программного обеспечения. 

> [!NOTE] 
> Независимый поставщик программного обеспечения должен предоставить доступ к группе ресурсов, в которой создается ресурс определения устройства. Ресурс определения устройства создается в подписке независимого поставщика программного обеспечения. Поэтому пользователю, группе пользователей или приложению в клиенте пользователя требуется доступ на чтение к этому ресурсу. 

После успешного завершения развертывания в **mainResourceGroup** будет создан ресурс устройства. Ресурс storageAccount будет создан в **managedResourceGroup**.

### <a name="create-the-managed-application-using-create-command"></a>Создание управляемого приложения с помощью команды create

Вы можете использовать команду `az managedapp create` для создания управляемых приложений из определения управляемого приложения. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

Свойство **appliance-definition-Id** — это идентификатор ресурса определения устройства, созданный на предыдущем шаге. Чтобы получить этот идентификатор, выполните следующую команду:

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

Эта команда возвращает определение устройства. Требуется значение свойства **идентификатора**.

Свойство **managed-rg-id** — это идентификатор группы ресурсов, где создаются ресурсы, определенные в файле applianceMainTemplate.json. Это управляемая группа ресурсов, которой управляет издатель. Если группа не существует, она создается автоматически.

Свойство **resource-group** — это группа ресурсов, в которой создается ресурс устройства. Ресурс Microsoft.Solutions/appliance размещается в этой группе ресурсов. 

**parameters** — параметры, необходимые для ресурсов, определенных в файле applianceMainTemplate.json.

## <a name="create-the-managed-application-using-portal"></a>Создание управляемого приложения с помощью портала

На портале также можно использовать управляемые приложения, опубликованные независимыми поставщиками программного обеспечения. Для этого выполните следующие действия.

На портале Azure в колонке "Создать" выберите "Управляемое приложение".

![](./media/managed-application-consumption/create-blade.png)

После этого появится список предложений от различных независимых поставщиков программного обеспечения и партнеров. Выберите то, которое вы хотите создать, и щелкните "Создать".

![](./media/managed-application-consumption/select-offer.png)

Затем в открывшейся колонке укажите параметры, необходимые для подготовки ресурсов. 

![](./media/managed-application-consumption/input-parameters.png)

После предоставления значений нажмите кнопку "ОК". Затем выполняется проверка входных данных. Если проверка прошла успешно, начнется развертывание шаблона. После завершения развертывания соответствующие ресурсы, определенные в шаблоне, подготавливаются в указанной группе управляемых ресурсов.

## <a name="known-issues"></a>Известные проблемы

В этом предварительном выпуске могут возникнуть следующие проблемы.

* При возникновении ошибки 500 (внутренняя ошибка сервера) во время создания устройства существует вероятность кратковременного сбоя. При возникновении этой проблемы повторите операцию.
* Для управляемой группы ресурсов требуется новая группа ресурсов. Использование существующей группы ресурсов приводит к сбою развертывания.
* Группу ресурсов, содержащую ресурс Microsoft.Solutions/appliances, нужно создать в расположении **westcentralus**.

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](managed-application-overview.md).
* См. сведения о [создании и публикации управляемого приложения Azure](managed-application-publishing.md) для поставщика.
