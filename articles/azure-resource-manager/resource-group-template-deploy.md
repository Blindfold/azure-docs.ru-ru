---
title: "Развертывание ресурсов с помощью PowerShell и шаблона | Документация Майкрософт"
description: "Узнайте, как использовать Azure Resource Manager и Azure PowerShell для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 093a63504843f63e25adb8b0247ebe82bc331061
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell

В этом разделе объясняется, как использовать Azure PowerShell с шаблонами Resource Manager для развертывания ресурсов в Azure. Если вы не знакомы с основными понятиями, связанными с развертыванием решений Azure и управлением ими, то см. [обзор Azure Resource Manager](resource-group-overview.md).

Развертываемый шаблон Resource Manager может быть локальным файлом на вашем компьютере или внешним файлом, расположенным в репозитории, например в GitHub. Шаблон, развертываемый в этой статье, доступен в разделе [Пример шаблона](#sample-template) или как [шаблон учетной записи хранения в GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install.md)]

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Развертывание шаблона с локального компьютера

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая служит в качестве контейнера для развертываемых ресурсов.
3. Разверните в группе ресурсов шаблон, определяющий ресурсы, которые необходимо создать.

Шаблон может включать параметры, которые позволяют настроить развертывание. Например, вы можете предоставить значения, предназначенные для конкретной среды (такой как среда разработки, тестирования и рабочая среда). Пример шаблона определяет параметр для номера SKU учетной записи хранения.

В следующем примере создается группа ресурсов и развертывается шаблон с локального компьютера:

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Развертывание шаблона из внешнего источника

Шаблоны Resource Manager можно хранить не на локальном компьютере, а на внешнем источнике. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **TemplateUri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, то можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](resource-manager-powershell-sas-token.md).

## <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров должен быть в указанном ниже формате.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Обратите внимание, что раздел parameters содержит имя параметра, которое совпадает с параметром, определенным в шаблоне (storageAccountType). Файл параметров содержит значение для параметра. Во время развертывания это значение автоматически передается в шаблон. Можно создать несколько файлов параметров для различных сценариев развертывания, а затем передать соответствующий файл параметров. 

Скопируйте предыдущий пример и сохраните его как файл с именем `storage.parameters.json`.

Чтобы передать локальный файл параметров, используйте параметр **TemplateParameterFile**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Чтобы передать внешний файл параметров, используйте параметр **TemplateParameterUri**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Вы можете использовать в ходе одной операции развертывания как встроенные параметры, так и локальный файл параметров. Например, часть значений можно указать в локальном файле параметров, а другую часть — в команде развертывания. Если значения для одного параметра указаны одновременно и в локальном файле параметров, и в командной строке, более высокий приоритет имеет значение из командной строки.

Тем не менее при использовании внешнего файла параметров нельзя передавать другие значения в командной строке или локальном файле. Если в параметре **TemplateParameterUri** указан файл параметров, все параметры командной строки игнорируются. Все значения параметров следует указать во внешнем файле. Если в шаблоне используется конфиденциальное значение, которое нельзя включать в файл параметров, то его можно передать в хранилище ключей. Кроме того, можно динамически указать значения всех параметров в командной строке.

Если шаблон содержит параметр, имя которого совпадает с именем одного из параметров в команде PowerShell, параметр из шаблон отображается с постфиксом **FromTemplate**. Предположим, что параметр **ResourceGroupName** в шаблоне конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

## <a name="test-a-template-deployment"></a>Тестовое развертывание шаблона

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Если ошибок не обнаружено, то команда завершается без ответа. Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, при попытке передать недопустимое значение для номера SKU учетной записи хранения возвращается следующая ошибка:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Если шаблон содержит синтаксическую ошибку, то команда возвращает сообщение об ошибке, указывающее, что ей не удалось проанализировать шаблон. В сообщении указывается номер строки и позиция ошибки синтаксического анализа.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Чтобы использовать полный режим, используйте параметр `Mode`.

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Пример шаблона

Для примеров в этой статье используется следующий шаблон. Скопируйте и сохраните его как файл с именем storage.json. Сведения о создании этого шаблона см. в статье [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* В примерах этой статьи ресурсы развертываются в группу ресурсов в подписке по умолчанию. Чтобы использовать другую подписку, см. статью [Управление несколькими подписками Azure](/powershell/azure/manage-subscriptions-azureps).
* Полный пример сценария, развертывающего шаблон, см. в статье [Сценарий для развертывания шаблона Resource Manager](resource-manager-samples-powershell-deploy.md).
* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).


