---
title: "Выбор признаков в процессе обработки и анализа данных группы | Документация Майкрософт"
description: "Описывает цели реконструирования характеристик и содержит примеры, поясняющие его роль в совершенствовании данных в процессе машинного обучения."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: zhangya;bradsev
translationtype: Human Translation
ms.sourcegitcommit: a6bc79b2cb5b73109cddd6cf57caeba754b52e2e
ms.openlocfilehash: a30620c8a6ec52fd9b3268c4c83f89181cf6d9f5
ms.lasthandoff: 12/20/2016


---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Выбор характеристик в процессе обработки и анализа данных группы (TDSP)
В этой статье описаны цели выбора характеристик и приводятся примеры, поясняющие его роль в совершенствовании данных в процессе машинного обучения. Эти примеры взяты из Студии машинного обучения Azure. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Проектирование и выбор признаков являются частью процесса TDSP, описанного в статье [Жизненный цикл процесса обработки и анализа данных группы](data-science-process-overview.md). Проектирование и выбор характеристик входят в этап **разработки характеристик** процесса TDSP.

* **Реконструирование признаков**: этот процесс направлен на создание дополнительных признаков на основе соответствующих существующих необработанных признаков и повышение эффективности прогнозирования алгоритма обучения.
* **Выбор признаков**: в этом процессе выбирается ключевое подмножество исходных признаков с целью сокращения размерности задачи обучения.

Как правило, **проектирование признаков** сначала применяется для создания дополнительных признаков, а затем выполняется на этапе **выбора признаков**, чтобы исключить несоответствующие, избыточные или сильно коррелирующие признаки.

## <a name="filtering-features-from-your-data---feature-selection"></a>Фильтрация признаков в данных. Выбор признаков
Выбор признаков — это процесс, который часто применяется для создания обучающих наборов данных для задач прогнозирующего моделирования, например задач классификации или регрессии. Целью этого является выбор подмножества признаков из исходного набора данных, уменьшение его размеров с помощью минимального набора признаков для представления максимального отклонения в данных. Это подмножество признаков является именно тем подмножеством, которое должно быть включено в обучение модели. Выбор признаков служит двум основным целям.

* Во-первых, выбор признаков часто повышает точность классификации, исключая несоответствующие, избыточные или сильно коррелирующие признаки.
* Во-вторых, он сокращает число признаков, что повышает эффективность процесса обучения модели. Это особенно важно для ученик, затратных в плане обучения, таких как вспомогательные векторные машины.

Несмотря на то, что выбор признаков нацелен на сокращение числа признаков в наборе данных, используемых для обучения модели, он зачастую не связан с термином «сокращение размерности». Методы выбора признаков извлекают поднабор из исходных признаков в данных без каких-либо изменений.  Методы сокращения размерности используют реконструированные признаки, которые могут преобразовывать исходные признаки и соответственно изменять их. Примеры методов сокращения размерности: анализ главных компонентов, анализ канонических корреляций и сингулярная декомпозиция.

В частности одна из распространенных категорий методов выбора признаков в контролируемом контексте называется «Выбор признаков на основе фильтра». Эти методы применяют статистическую меру для назначения рейтинга каждому признаку путем вычисления корреляции между каждым признаком и целевым атрибутом. Затем признаки ранжируются по рейтингу, который может использоваться, чтобы задать пороговое значение для сохранения или исключения конкретных признаков. Примеры статистических показателей, используемых в этих методах: корреляция Пирсона, взаимная информация и критерий хи-квадрат.

В Студии машинного обучения Azure предусмотрены модули для выбора признаков. Как показано на следующем рисунке, это модули [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра) и [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis] (Линейный дискриминант Фишера).

![Пример выбора признаков](./media/machine-learning-data-science-select-features/feature-Selection.png)

Рассмотрим, например, использование модуля [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра). Для удобства мы будем продолжать использовать описанный выше пример интеллектуального анализа текста. Допустим, что мы хотим создать регрессионную модель после создания набора из 256 признаков с помощью модуля [Feature Hashing][feature-hashing] (Хэширование признаков) и что выходная переменная находится в столбце Col1 и представляет собой рейтинги рецензий на книги в диапазоне от 1 до 5. Установив для параметра «Метод количественной оценки» значение «Корреляция Пирсона», параметра «Целевой столбец» — «Col1» и для параметра «Число необходимых признаков» — значение 50. Затем модуль [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра) создаст набор данных, содержащий 50 признаков с целевым атрибутом Col1. На следующем рисунке показан только что описанный процесс этого эксперимента и входные параметры.

![Пример выбора признаков](./media/machine-learning-data-science-select-features/feature-Selection1.png)

На следующем рисунке показаны результирующие наборы данных. Каждый признак оценивается на основе корреляции Пирсона между ним и целевым атрибутом «Col1». Сохраняются признаки с наибольшей оценкой.

![Пример выбора признаков](./media/machine-learning-data-science-select-features/feature-Selection2.png)

На следующем рисунке показаны соответствующие оценки для выбранных признаков.

![Пример выбора признаков](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Применяя модуль [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра), будут выбраны 50 из 256 признаков, поскольку эти признаки максимально коррелируют с целевой переменной Col1 на основе метода оценки "Корреляция Пирсона".

## <a name="conclusion"></a>Заключение
Реконструирование характеристик и выбор характеристик — два часто выполняемых действия. Реконструированные и выбранные характеристики повышают эффективность процесса обучения, который пытается извлечь ключевые сведения, содержащиеся в данных. Они также дают возможность повысить возможности этих моделей, чтобы точнее классифицировать входные данные и более надежно предсказывать нужные результаты. Реконструирование и выбор признаков можно также объединять, чтобы сделать процесс обучения более алгоритмизируемым. Этого можно достичь путем расширения и сокращения числа признаков, необходимых для калибровки или обучения модели. С точки зрения математики выбранные для обучения модели признаки являются минимальным набором независимых переменных, которые определяют структуры в данных и затем успешно прогнозируют результаты.

Обратите внимание, что он не всегда обязателен для выполнения реконструирования или выбора признаков. Его необходимость зависит от собираемых данных, используемого алгоритма и цели эксперимента.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/


