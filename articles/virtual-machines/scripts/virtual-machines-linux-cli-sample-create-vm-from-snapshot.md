---
title: "Пример скрипта Azure CLI. Создание виртуальной машины из моментального снимка | Документация Майкрософт"
description: "Создание виртуальной машины из моментального снимка с помощью скрипта Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b5e33d7dcee5b5241de796d632a9b6069b991f7b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Создание виртуальной машины из моментального снимка с помощью интерфейса командной строки

Этот скрипт создает виртуальную машину из моментального снимка диска ОС.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Создание виртуальной машины из моментального снимка")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Для создания управляемого диска, виртуальной машины и всех связанных ресурсов этот скрипт использует указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Возвращает моментальный снимок на основе имени моментального снимка и группы ресурсов. Для создания управляемого диска используется свойство идентификатора возвращаемого объекта.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Создает управляемые диски из моментального снимка на основе идентификатора моментального снимка, имени диска, типа хранилища и размера.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Создает виртуальную машину с помощью управляемого диска ОС. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов интерфейса командной строки для виртуальных машин см. в [документации по виртуальным машинам Azure под управлением Linux](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

