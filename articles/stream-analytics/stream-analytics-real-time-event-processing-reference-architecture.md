---
title: "Обработка событий средствами Stream Analytics в режиме реального времени | Документация Майкрософт"
description: "Узнайте о том, как обрабатывать и анализировать события в режиме реального времени с помощью различных служб Azure."
keywords: "обработка в режиме реального времени, обработка событий, эталонная архитектура"
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: 
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8d2449f84ca0caf85e22a91e14f04ea3c329be03
ms.lasthandoff: 05/01/2017


---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Эталонная архитектура: обработка событий в режиме реального времени средствами Microsoft Azure Stream Analytics
Эталонная архитектура для обработки событий в режиме реального времени с помощью инструментов Azure Stream Analytics определяет общие принципы развертывания решения для потоковой обработки данных в реальном времени в рамках модели «платформа как услуга» (platform as a service, PaaS) на базе службы Microsoft Azure.

## <a name="summary"></a>Сводка
Традиционно в основе аналитических решений лежат такие функциональные возможности и компоненты, как извлечение, преобразование и загрузка информации, а также хранилища данных, в которые информация заносится до анализа. Изменение требований, в том числе повышение скорости поступления данных, привело к тому, что существующая модель себя исчерпала. Одним из решений является анализ данных в составе динамических потоков перед их сохранением. Эта возможность не нова, однако такой подход не получил широкого распространения во всех направлениях отрасли. 

Платформа Microsoft Azure предлагает богатый ассортимент аналитических технологий, позволяющих реализовать самые разные сценарии и решения с различными требованиями. Выбор оптимальной службы Azure для развертывания комплексного решения может оказаться непростой задачей с учетом всех возможных вариантов. В этом документе описаны возможности и принципы взаимодействия различных служб Azure, позволяющих реализовать решение для потоковой обработки событий. В нем также рассматриваются возможные сценарии эффективного использования этого подхода клиентами.

## <a name="contents"></a>Оглавление
* Аннотация
* Общие сведения об аналитике в режиме реального времени
* Ценность средств Azure для работы с данными в режиме реального времени
* Распространенные сценарии применения средств аналитики в режиме реального времени
* Архитектура и компоненты
  * Источники данных
  * Уровень интеграции данных
  * Уровень аналитики в режиме реального времени
  * Уровень хранения данных
  * Уровень представления или потребления
* Заключение

**Автор:** Чарльз Феддерсен (Charles Feddersen), архитектор решений, Научно-инновационный центр анализа данных (Data Insights Center of Excellence), корпорация Майкрософт

**Опубликовано:** январь 2015 г.

**Версия:** 1.0

**Ссылка для загрузки:** [Обработка событий в режиме реального времени средствами Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Справка
Дополнительную помощь и поддержку вы можете получить на нашем [форуме Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-get-started.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


