---
title: "Ограничения в базе данных Azure для MySQL | Документация Майкрософт"
description: "Описываются ограничения (предварительная версия) в базе данных Azure для MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c19304ec2605faa3d69e82ac41d26ec54fee6ac7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Ограничения в базе данных Azure для MySQL (предварительная версия)
Служба базы данных Azure для MySQL работает в режиме общедоступной предварительной версии. В следующих разделах описываются действующие ограничения емкости и функциональных возможностей в службе базы данных.

## <a name="service-tier-maximums"></a>Максимумы уровней служб
База данных Azure для MySQL имеет несколько уровней служб, которые можно выбрать при создании сервера. Дополнительные сведения см. в статье [Уровни служб в базе данных Azure для MySQL](concepts-service-tiers.md).  

На каждом уровне служб в предварительной версии службы существует максимальное число подключений, единиц вычислений и максимальный объем хранилища. Ниже перечислены эти ограничения. 

|                            |                   |
| :------------------------- | :---------------- |
| **Максимальное число подключений**        |                   |
| Базовый, 50 единиц вычислений     | 50 подключений    |
| Базовый, 100 единиц вычислений    | 100 подключений   |
| Стандартный, 100 единиц вычислений | 200 подключений   |
| Стандартный, 200 единиц вычислений | 300 подключений   |
| Стандартный, 400 единиц вычислений | 400 подключений   |
| Стандартный, 800 единиц вычислений | 500 подключений   |
| **Максимальное число единиц вычислений**      |                   |
| Уровень службы "Базовый"         | 100 единиц вычислений |
| Уровень службы "Стандартный"      | 800 единиц вычислений |
| **Максимальный объем хранилища**            |                   |
| Уровень службы "Базовый"         | 1 TБ              |
| Уровень службы "Стандартный"      | 1 TБ              |

Когда число подключений превышается, может появиться следующая ошибка:
> ОШИБКА 1040 (08004): Слишком много подключений

## <a name="preview-functional-limitations"></a>Ограничения функциональных возможностей предварительной версии
### <a name="scale-operations"></a>Операции масштабирования:
1.    В настоящее время динамическое масштабирование серверов между уровнями служб не поддерживается. Имеется в виду переключение между уровнями служб "Базовый" и "Стандартный".
2.    В настоящее время динамическое увеличение объема хранилища по запросу на предварительно созданном сервере не поддерживается.
3.    Уменьшение размера хранилища сервера не поддерживается.

### <a name="server-version-upgrades"></a>Обновление версии сервера:
- В настоящее время автоматический переход между основными версиями ядра СУБД не поддерживается.

### <a name="subscription-management"></a>Управление подпиской:
- В настоящее время динамическое перемещение предварительно созданных серверов между подпиской и группой ресурсов не поддерживается.

### <a name="point-in-time-restore"></a>Восстановление до точки во времени:
1.    Восстановление в другой уровень служб и (или) до другого числа единиц вычислений и размера хранилища не допускается.
2.    Восстановление удаленного сервера не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия
[Уровни служб в базе данных Azure для MySQL](concepts-service-tiers.md)
[Поддерживаемые версии в базе данных Azure для MySQL](concepts-supported-versions.md)

