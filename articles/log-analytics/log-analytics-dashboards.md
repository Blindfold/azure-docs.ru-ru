---
title: "Создание пользовательской панели мониторинга в Azure Log Analytics | Документация Майкрософт"
description: "Это руководство поможет вам понять, как панели мониторинга в службе Log Analytics помогают следить за состоянием вашей среды с помощью визуализации всех ваших сохраненных запросов поиска по журналам."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: a8c9766bf066a7f0dfd28ebb4e41bf0eaf3f05bd
ms.contentlocale: ru-ru
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Создание пользовательской панели мониторинга для Log Analytics
Это руководство поможет вам понять, как панели мониторинга в службе Log Analytics помогают следить за состоянием вашей среды с помощью визуализации всех ваших сохраненных запросов поиска по журналам.

![Пример панели мониторинга](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Все пользовательские панели мониторинга, создаваемые на портале OMS, также доступны в мобильном приложении OMS. Дополнительные сведения о приложениях см. на следующих страницах.

* [Мобильное приложение OMS в Microsoft Store](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Мобильное приложение OMS в Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![панель мониторинга на мобильном устройстве](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Создание панели мониторинга
Чтобы начать, перейдите на страницу обзора OMS. Слева вы увидите плитку **My Dashboard** (Моя панель мониторинга). Щелкните ее, чтобы открыть свою панель мониторинга.

![Обзор](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Добавление плитки
В разделе панелей мониторинга сведения на плитках берутся из сохраненных запросов поиска журналов. В службе OMS есть много уже готовых запросов поиска журналов, поэтому вы сможете сразу приступить к созданию панели мониторинга. Чтобы начать работу, следуйте инструкциям ниже.

В представлении My Dashboard (Моя панель мониторинга) щелкните **Customize** (Настроить) для входа в режим настройки.

![Инструкция в виде рисунков](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Справа на странице откроется панель со всеми вашими сохраненными запросами поиска журналов в рабочей области. Чтобы представить сохраненный поиск по журналу в виде плитки, наведите указатель мыши на сохраненный поисковой запрос, а затем щелкните знак **плюс**.

![Добавление плиток 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Если щелкнуть знак **плюс**, в представлении My Dashboard (Моя панель мониторинга) появится новая плитка.

![Добавление плиток 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Изменение плитки
В представлении My Dashboard (Моя панель мониторинга) щелкните **Customize** (Настроить) для входа в режим настройки. Щелкните плитку, которую нужно изменить. На панели справа появятся параметры, которые можно изменять.

![Изменение плитки](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Изменение плитки](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Визуализация плиток
Для плиток на выбор предлагаются три варианта визуализации:

| тип диаграммы | назначение |
| --- | --- |
| ![Линейчатая диаграмма](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Отображает временную шкалу результатов сохраненного поиска по журналу в виде линейчатой диаграммы или списка результатов по полям (зависит от того, сортирует ли поиск по журналу найденные результаты по полям). |
| ![метрика](./media/log-analytics-dashboards/oms-dashboards-metric.png) |На ней показывается число, которое означает, сколько раз был найден элемент по запросу поиска журнала. Плитки с метрикой позволяют задать пороговое значение, по достижении которого плитка будет выделяться. |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |Отображает временную шкалу совпадения результатов сохраненного поиска по журналу со значениями в виде графика. |

### <a name="threshold"></a>Пороговое значение
Чтобы задать для плитки пороговое значение, используйте визуализацию в виде метрики. Установите для параметра Threshold (Пороговое значение) значение On (Включено). Укажите, следует ли выделять плитку, когда достигнуто пороговое значение, и в поле ниже задайте требуемое значение.

## <a name="organizing-the-dashboard"></a>Упорядочение панели мониторинга
Чтобы упорядочить панель мониторинга, откройте представление My Dashboard (Моя панель мониторинга) и щелкните **Customize** (Настроить), чтобы войти в режим настройки. Выберите и перетащите плитку в нужное место.

![Упорядочение панели мониторинга](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Удаление плитки
Чтобы удалить плитку, откройте представление My Dashboard (Моя панель мониторинга) и щелкните **Customize** (Настроить), чтобы войти в режим настройки. Выделите плитку, которую требуется удалить, а затем на панели справа нажмите кнопку **Remove Tile** (Удалить плитку).

![Удаление плитки](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Дальнейшие действия
* Создайте [оповещения](log-analytics-alerts.md) в службе Log Analytics для создания уведомлений и устранения проблем.

