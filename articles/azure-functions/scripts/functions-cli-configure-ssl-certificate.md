---
title: "Пример сценария Azure CLI. Привязка настраиваемого SSL-сертификата к приложению-функции | Документация Майкрософт"
description: "Пример сценария Azure CLI для привязки настраиваемого SSL-сертификата к приложению-функции в Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/10/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 9b7f84f5ad01335a3ce72b6b2aa654968fdd9499
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---
# <a name="bind-a-custom-ssl-certificate-to-a-function-app"></a>Привязка пользовательского SSL-сертификата к приложению-функции

Этот пример сценария создает в службе приложений приложение-функции со связанными ресурсами, а затем привязывает к нему SSL-сертификат имени личного домена. Для этого примера потребуется:

* Доступ к странице конфигурации DNS вашего регистратора доменных имен.
* Допустимый PFX-файл и пароль для SSL-сертификата, который будет отправлен и привязан.

Чтобы привязать SSL-сертификат, приложение-функция должно быть создано в плане службы приложений, а не в плане потребления.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Привязка SSL-сертификата к веб-приложению")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений, необходимый для привязки SSL-сертификатов. |
| [az functionapp create]() | Создает приложение-функцию. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Сопоставляет личный домен с приложением-функцией. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Отправляет SSL-сертификат в приложение-функцию. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Привязывает отправленный SSL-сертификат к приложению-функции. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure]().

