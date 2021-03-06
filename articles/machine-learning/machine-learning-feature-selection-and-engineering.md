---
title: "Конструирование и выбор признаков в машинном обучении Azure | Документация Майкрософт"
description: "В этой статье объясняются цели выбора и конструирования признаков и приводятся примеры того, как эти процессы влияют на совершенствование данных в машинном обучении."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 9ceb524d-842e-4f77-9eae-a18e599442d6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/18/2017
ms.author: zhangya;bradsev
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: machine-learning-data-science-create-features
translationtype: Human Translation
ms.sourcegitcommit: ba61d00f277af579c87a130336ead9879b82a6de
ms.openlocfilehash: c6b88355df430e78594fc1283c9df01ad6e27e20


---
# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Реконструирование и выбор признаков в машинном обучении Azure
В этой статье объясняются цели конструирования и выбора признаков в процессе совершенствования данных в машинном обучении. На примерах, предоставляемых студией машинного обучения Azure, показаны составляющие этих процессов.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Обучающие данные, используемые в машинном обучении, можно улучшить путем выбора или извлечения признаков из собранных необработанных данных. Пример конструирования признака в контексте обучения для классификации изображений рукописных символов содержит карту битовой плотности, построенную на основе необработанных данных распределения битов. Эта карта помогает более эффективно находить края символов, чем в случае необработанных данных о распределении.

Реконструированные и выбранные признаки повышают эффективность процесса обучения, который пытается извлечь ключевые сведения, содержащиеся в данных. Они также дают возможность повысить возможности этих моделей, чтобы точнее классифицировать входные данные и более надежно предсказывать нужные результаты. Реконструирование и выбор признаков можно также объединять, чтобы сделать процесс обучения более алгоритмизируемым. Этого можно достичь путем расширения и сокращения числа признаков, необходимых для калибровки или обучения модели. С точки зрения математики выбранные для обучения модели признаки являются минимальным набором независимых переменных, которые определяют структуры в данных и затем успешно прогнозируют результаты.

Реконструирование и выбор признаков является частью большего процесса, который обычно состоит из четырех этапов:

* Сбор данных
* совершенствование данных;
* построение модели;
* постобработка.

Конструирование и выбор признаков относятся к этапу совершенствования данных в машинном обучении. Для наших целей можно выделить три следующих аспекта этого процесса.

* **Предварительная обработка данных**. Этот процесс должен гарантировать, что собраны чистые и согласованные данные. Он предусматривает объединение наборов данных, обработку отсутствующих данных, обработку несогласованных данных и преобразование типов данных.
* **Конструирование признаков**. Этот процесс направлен на создание дополнительных признаков на основе соответствующих существующих необработанных признаков и повышение эффективности прогнозирования алгоритма обучения.
* **Выбор признаков**. В этом процессе выбирается ключевое подмножество исходных признаков с целью сокращения размерности задачи обучения.

В этом разделе описываются только аспекты реконструирования признаков и выбора признаков в процессе совершенствования данных. Дополнительную информацию об этапе предварительной обработки данных см. в видео [Preprocessing Data in Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) (Предварительная обработка данных в студии машинного обучения Azure).

## <a name="creating-features-from-your-data--feature-engineering"></a>Создание признаков из данных. Конструирование признаков
Обучающие данные образуют матрицу из примеров (записей или наблюдений, хранимых в строках), каждый из которых имеет набор признаков (переменных или полей, хранящихся в столбцах). Признаки, указанные в схеме эксперимента должны характеризовать закономерности в данных. Несмотря на то что многие поля необработанных данных можно напрямую включить в набор выбранных признаков, используемых для обучения модели, часто для формирования усовершенствованного набора данных для обучения дополнительные сконструированные признаки требуется создавать из признаков в необработанных данных.

Какие признаки нужно создавать для усовершенствования набора данных при обучении модели? Реконструированные признаки, совершенствующие обучение, содержат сведения, которые лучше выделяют закономерности в данных. Новые признаки должны предоставлять дополнительные сведения, нечетко зафиксированные или не очевидные в исходном или существующем наборе, но это довольно сложный процесс. Обоснованные и эффективные решения часто требуют определенного знания предметной области.

Начиная работу с машинным обучением Azure, этот процесс проще всего понять с помощью примеров, которые поставляются в комплекте со студией машинного обучения. Здесь представлены два примера.

* Пример регрессии ([прогнозирование количества сдаваемых напрокат велосипедов](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) в контролируемом эксперименте с известными целевыми значениями.
* Пример классификации интеллектуального анализа текста с использованием [хэширования признаков][feature-hashing].

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Пример 1. Добавление временных признаков для регрессионной модели
Воспользуемся экспериментом "Прогнозирование спроса на велосипеды" в Студии машинного обучения Azure, чтобы продемонстрировать реконструкцию признаков для задачи регрессии. Цель этого эксперимента — прогноз спроса на велосипеды, то есть количество сдаваемых напрокат велосипедов в конкретный месяц, день или час. **Набор данных по прокату велосипедов UCI** используется в качестве необработанных входных данных.

Этот набор данных основывается на реальных данных компании Capital Bikeshare, которая содержит сеть проката велосипедов в городе Вашингтоне (США). Набор данных представляет количество сдаваемых напрокат велосипедов в определенный час дня в 2011–2012 гг. и содержит 17 379 строк и 17 столбцов. Набор необработанных признаков содержит погодные условия (температура, влажность и скорость ветра) и тип дня (выходной или будний день). Поле для прогнозирования **cnt** — количество сдаваемых напрокат велосипедов в конкретный час, которое меняется в диапазоне от 1 до 977.

Для создания эффективных признаков в обучающих данных строятся четыре регрессионные модели с использованием одного и того же алгоритма, но с четырьмя разными наборами данных для обучения. Четыре набора данных представляют одни и те же необработанные входные данные, но с увеличивающимся числом набора признаков. Эти признаки сгруппированы в четыре категории:

1. А = «погода» + «праздник» + «рабочий день» + «выходной день» для прогнозируемого дня
2. Б = количество велосипедов, которые сданы напрокат в каждый из предыдущих 12 часов
3. В = количество велосипедов, которые были сданы напрокат в каждый из предыдущих 12 дней в течение одного и того же часа
4. Г = количество велосипедов, которые были сданы напрокат в каждую из предыдущих 12 недель в течение одного и того же часа и дня

Помимо набора признаков А, который уже существует в исходных необработанных данных, другие три набора признаков создаются в процессе реконструирования признаков. Набор признаков Б охватывает новейший спрос на велосипеды. Набор признак В спрос на велосипеды в конкретный час. Набор признаков Г охватывает спрос на велосипеды в определенный час и определенный день недели. Каждый из четырех наборов данных для обучения содержит наборы признаков А, А + Б, А + Б + В и A + Б + В + Г соответственно.

В эксперименте машинного обучения Azure эти четыре набора данных для обучения формируются через четыре ветви предварительно обработанного входного набора данных. За исключением крайней левой ветви, каждая из этих ветвей содержит модуль [Выполнить сценарий R][execute-r-script], в котором набор производных признаков (набор признаков Б, В и Г) соответственно конструируется и добавляется в импортируемый набор данных. На следующем рисунке показан сценарий R, используемый для создания набора признаков Б во второй слева ветви.

![Создание набора признаков](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

Сравнение результатов производительности четырех моделей приведено в таблице ниже. Наилучшие результаты даются набором признаков А + Б + В. Обратите внимание, что частота ошибок уменьшается, когда в обучающие данные включается дополнительный набор признаков. Это подтверждает наше предположение, что наборы признаков Б и В предоставляют дополнительные данные для задачи регрессии. Добавление набора признаков Г не дает дополнительного сокращения частоты ошибок.

![Сравнение результатов производительности](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> Пример 2. Создание признаков в интеллектуальном анализе текста
Реконструирование признаков широко применяется в задачах, связанных с интеллектуальным анализом текста, например классификации документов и анализе тональностей. Например, если вы хотите классифицировать документы по нескольким категориям, типичным предположением является следующее: слова или фразы, которые встречаются в одной категории документов, с меньшей вероятностью встречаются в другой категории. Иными словами, частота распределения слов или фраз может характеризовать разные категории документов. В приложениях интеллектуального анализа текста отдельные части текстового содержимого обычно служат в качестве входных данных, поэтому для создания признаков, связанных с частотой слова или фразы, необходим процесс конструирования признаков.

Для выполнения этой задачи вызывается метод *хэширования признаков*, чтобы эффективно превратить произвольные признаки текста в индексы. Вместо того чтобы сопоставлять каждый признак текста (слова или фразы) для определенного индекса, этот метод применяет хэш-функции к признакам и непосредственно использует их хэш-значения как индексы.

В Машинном обучении Azure есть модуль [Feature Hashing][feature-hashing] (Хэширование признаков), с помощью которого можно создавать признаки этих слов или фраз. На рисунке ниже показан пример использования этого модуля. Входной набор данных содержит два столбца: рейтинг книги от 1 до 5 и содержимое фактической рецензии. Задача этого модуля [хэширования признаков][feature-hashing] состоит в извлечении новых признаков, чтобы показать частоту вхождения соответствующих слов или фраз в рецензии на определенную книгу. Чтобы использовать этот модуль, необходимо выполнить такие действия.

1. Выберите столбец, содержащий входной текст (в этом примере — **Col2**).
2. В поле *Hashing bitsize* (Размер бита хэширования) задайте значение 8, означающее, что создается 2^8 = 256 признаков. Слово или фраза в тексте будут хэшированы по 256 индексам. Параметр *Hashing bitsize* меняется в диапазоне от 1 до 31. Если для этого параметра установлено большее значение, менее вероятно, что слова или фразы будут хэшироваться по тому же индексу.
3. Задайте для параметра *N-grams* значение 2. Этот параметр возвращает частоту вхождения униграмм (признаков для каждого отдельного слова) и биграмм (признаков для каждой пары смежных слов) во входном тексте. Значение параметра *N-grams* меняется в диапазоне от 0 до 10, определяя максимальное количество последовательно идущих слов, которые включены в признак.  

![Модуль хэширования признаков](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

На следующем рисунке показано, на что похожи эти новые признаки.

![Пример хэширования признаков](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Фильтрация признаков в данных. Выбор признаков
*Выбор признаков* — это процесс, с помощью которого часто создаются наборы данных для обучения для задач прогнозирующего моделирования, например задач классификации или регрессии. Целью этого является выбор подмножества признаков из исходного набора данных для уменьшения его размеров с помощью минимального набора признаков для представления максимального отклонения в данных. Это подмножество признаков содержит только те признаки, которые должны быть включены в обучение модели. Выбор признаков служит двум основным целям.

* Выбор признаков часто повышает точность классификации, исключая несоответствующие, избыточные или сильно коррелирующие признаки.
* Выбор признаков сокращает число признаков, что повышает эффективность процесса обучения модели. Это особенно важно для ученик, затратных в плане обучения, таких как вспомогательные векторные машины.

Несмотря на то что выбор признаков нацелен на сокращение числа признаков в наборе данных, используемых для обучения модели, он зачастую не связан с термином *сокращение размерности*. Методы выбора признаков извлекают поднабор из исходных признаков в данных без каких-либо изменений.  Методы сокращения размерности используют реконструированные признаки, которые могут преобразовывать исходные признаки и соответственно изменять их. Примеры методов сокращения размерности: анализ главных компонентов, анализ канонических корреляций и сингулярная декомпозиция.

Одна из распространенных категорий методов выбора признаков в контролируемом контексте называется "Выбор признаков на основе фильтра". Эти методы применяют статистическую меру для назначения рейтинга каждому признаку путем вычисления корреляции между каждым признаком и целевым атрибутом. Затем признаки ранжируются по рейтингу, который может использоваться, чтобы задать пороговое значение для сохранения или исключения конкретных признаков. Примеры статистических показателей, используемых в этих методах: корреляция Пирсона, взаимная информация и критерий хи-квадрат.

В студии машинного обучения Azure предусмотрены модули для выбора признаков. Как показано на следующем рисунке, это модули [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра) и [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis] (Линейный дискриминант Фишера).

![Пример выбора признаков](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)

Например, используйте модуль [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра) с примером интеллектуального анализа текста, описанным выше. Допустим, вам необходимо построить регрессионную модель после создания набора из 256 признаков с помощью модуля [Feature Hashing][feature-hashing] (Хэширование признаков). При этом выходная переменная находится в столбце **Col1** и представляет собой рейтинг рецензий на книгу в диапазоне от 1 до 5. Установите для параметра **Feature scoring method** (Метод оценки признаков) значение **Pearson Correlation** (Корреляция Пирсона), для параметра **Target column** (Целевой столбец) — **Col1**, а для параметра **Number of desired features** (Число необходимых признаков) — значение **50**. Затем модуль [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра) создает набор данных, содержащий 50 признаков с целевым атрибутом **Col1**. На следующем рисунке показан процесс этого эксперимента и входные параметры.

![Пример выбора признаков](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

На следующем рисунке показаны итоговые наборы данных. Каждый признак оценивается на основе корреляции Пирсона между самим признаком и целевым атрибутом **Col1**. Сохраняются признаки с наибольшей оценкой.

![Наборы данных для выбора признаков на основе фильтра](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

На следующем рисунке показаны соответствующие оценки для выбранных признаков.

![Оценки выбранных признаков](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Применяя этот модуль [Filter-Based Feature Selection][filter-based-feature-selection] (Выбор признаков на основе фильтра), будут выбраны 50 из 256 признаков, поскольку эти признаки максимально коррелируют с целевой переменной **Col1** на основе метода оценки **Корреляция Пирсона**.

## <a name="conclusion"></a>Заключение
Конструирование и выбор признаков — два часто выполняемых действия по подготовке обучающих данных при построении модели машинного обучения. Как правило, сначала применяется конструирование признаков для создания дополнительных признаков, а затем выполняется выбор признаков, чтобы исключить несоответствующие, избыточные или сильно коррелирующие признаки.

Не всегда обязательно выполнять реконструирование или выбор признаков. Необходимость в них зависит от существующих или собираемых данных, используемого алгоритма и цели эксперимента.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/



<!--HONumber=Dec16_HO2-->


