---
title: "Управление контейнерами томов StorSimple | Документация Майкрософт"
description: "В данной статье рассказывается, как на странице контейнеров томов службы диспетчера StorSimple добавить, изменить или удалить контейнер томов."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 1c64ce75-1fd3-4d3b-9304-d4dc0fc2b069
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/24/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4fb0f4e61ec98546e7044bf760d24a1ba932fe5b


---
# <a name="use-the-storsimple-manager-service-to-manage-storsimple-volume-containers"></a>Использование службы диспетчера StorSimple для управления контейнерами томов StorSimple
## <a name="overview"></a>Обзор
В данном учебнике рассказывается, как с помощью службы диспетчера StorSimple создавать контейнеры томов StorSimple и управлять ими.

Контейнер томов в устройстве Microsoft Azure StorSimple содержит один или несколько томов, которые совместно используют учетную запись хранения, функцию шифрования и параметры потребления полосы пропускания. В устройстве может быть несколько контейнеров томов для всех его томов. 

У контейнера томов имеются указанные ниже атрибуты.

* **Тома** — многоуровневые или локально закрепленные тома StorSimple, содержащиеся в контейнере томов. Контейнер томов может содержать до 256 томов StorSimple.
* **Шифрование** — ключ шифрования, который можно задать для каждого контейнера томов. Этот ключ используется для шифрования данных, отправляемых с устройства StorSimple в облако. Для шифрования используется алгоритм AES-256 (оборонного класса), использующий вводимый пользователем ключ. Для защиты данных, размещаемых в облаке, рекомендуется всегда шифровать их.
* **Учетная запись хранения** — учетная запись хранения, связанная с поставщиком услуг облачного хранилища. Все тома, находящиеся в контейнере томов, совместно используют эту учетную запись хранения. При создании контейнера томов можно выбрать учетную запись хранения из существующего списка или создать новую, а затем указать учетные данные доступа для этой учетной записи.
* **Пропускная способность облака** — часть пропускной способности, потребляемая устройством при передаче данных из устройства в облако. При настройке контейнера можно ограничить пропускную способность, указав значение от 1 до 1000 Мбит/с. Если необходимо, чтобы устройство потребляло всю доступную полосу пропускания, выберите для этого поля значение "Не ограничено". Кроме того, можно создать и применить шаблон пропускной способности для распределения пропускной способности по расписанию.

![Страница контейнеров томов](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Ниже рассказывается, как на странице **Контейнеры томов** StorSimple выполнять указанные ниже стандартные операции.

* Добавление контейнера томов 
* Изменение контейнера томов 
* Удаление контейнера томов 

## <a name="add-a-volume-container"></a>Добавление контейнера томов
Чтобы добавить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]

## <a name="modify-a-volume-container"></a>Изменение контейнера томов
Чтобы изменить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Удаление контейнера томов
Контейнер томов содержит тома. Чтобы удалить контейнер, необходимо сначала удалить из него все тома. Чтобы удалить контейнер томов, выполните указанные ниже действия.

[!INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [управлении томами StorSimple](storsimple-manage-volumes.md). 
* Узнайте больше об [использовании службы диспетчера StorSimple для администрирования устройства StorSimple](storsimple-manager-service-administration.md).




<!--HONumber=Nov16_HO3-->


