---
title: "Единицы запроса и оценка пропускной способности (Azure DocumentDB) | Документация Майкрософт"
description: "Узнайте о том, как понять факторы, задать количество запросов и оценить количество необходимых единиц запросов в DocumentDB."
services: documentdb
author: syamkmsft
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6185c703e9148c71d9995b92540b8ea72fba5cc0
ms.lasthandoff: 04/18/2017


---
# <a name="request-units-in-documentdb"></a>Единицы запросов в DocumentDB
Теперь доступен [калькулятор единиц запроса](https://www.documentdb.com/capacityplanner) DocumentDB. Дополнительные сведения см. в разделе [Оценка требований к пропускной способности](documentdb-request-units.md#estimating-throughput-needs).

![Калькулятор пропускной способности][5]

## <a name="introduction"></a>Введение
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) — это полностью управляемая, масштабируемая служба базы данных NoSQL для документов JSON. Благодаря DocumentDB вам не нужно арендовать виртуальные машины, развертывать программное обеспечение или отслеживать работу баз данных. Система DocumentDB обслуживается и непрерывно контролируется инженерами корпорации Майкрософт, что позволяет добиться доступности, производительности и защиты данных мирового уровня. Данные в DocumentDB хранятся в коллекциях, которые представляют собой эластичные контейнеры с высокой доступностью. Вместо того чтобы планировать аппаратные ресурсы для коллекции, такие как ЦП, память и операции ввода-вывода, и управлять ими, вы можете зарезервировать пропускную способность в виде запросов в секунду. База данных DocumentDB автоматически управляет подготовкой, прозрачным разделением и масштабированием коллекции для обслуживания подготовленного количества запросов. 

DocumentDB поддерживает ряд интерфейсов API для чтения, записи, запросов и выполнения хранимых процедур. Так как не все запросы одинаковы, им назначается нормализованное количество **единиц запроса** на основе объема вычислений, необходимых для обслуживания запроса. Количество единиц запроса для операции обусловлено. В заголовке ответа вы можете проверить количество единиц запросов, потребляемых любой операцией в DocumentDB.

Любую коллекцию в DocumentDB можно зарезервировать с пропускной способностью, которая выражается в единицах запроса. Пропускная способность исчисляется блоками по 100 единиц запросов в секунду (число запросов может быть разным: от сотен до миллионов в секунду). Для адаптации к меняющимся потребностям обработки и получения доступа к шаблонам вашего приложения подготовленную пропускную способность можно настраивать на протяжении жизненного цикла коллекции. 

Ознакомившись с данной статьей, вы сможете ответить на следующие вопросы.  

* Что такое единицы запросов и затраты на запросы?
* Как задать количество единиц запросов для одной коллекции?
* Как оценить приемлемое количество единиц запросов для моего приложения?
* Что произойдет, если превысить максимальное количество единиц запросов для одной коллекции?

## <a name="request-units-and-request-charges"></a>Единицы запросов и затраты на запросы
DocumentDB и API для MongoDB обеспечивают высокую прогнозируемую производительность за счет *резервирования* ресурсов для удовлетворения требований приложения к пропускной способности.  Так как нагрузка приложения и шаблоны доступа могут со временем меняться, DocumentDB позволяет легко увеличивать или уменьшать зарезервированную пропускную способность для приложения.

В DocumentDB зарезервированная пропускная способность указывается в обрабатываемых единицах запросов в секунду.  Так как пропускная способность зависит от единиц запросов, вы можете *зарезервировать* для приложения гарантированное количество единиц запросов в секунду.  Каждая операция в DocumentDB (например создание документа, выполнение запроса, обновление документа) сопровождается потреблением ресурсов ЦП, объема памяти и выполнением операций ввода-вывода.  Иными словами, при выполнении каждой операции взимается *плата за запрос*, которая выражается в *единицах запроса*.  Зная факторы, которые влияют на затраты единиц запросов, и требования приложения к пропускной способности, вы сможете значительно снизить затраты на работу приложения. 

Прежде чем приступить к работе, рекомендуется просмотреть следующий видеоролик, в котором Аравинд Рамачандран (Aravind Ramachandran) объясняет, что такое единицы запросов и прогнозируемая производительность в DocumentDB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-documentdb"></a>Указание количества единиц запроса в DocumentDB
При создании коллекции DocumentDB укажите количество единиц запросов в секунду (ЕЗ в секунду), которое вы хотите зарезервировать. На основе подготовленной пропускной способности база данных DocumentDB выделяет физические разделы для размещения коллекции и разделяет или перераспределяет данные между разделами по мере увеличения их объема.

Для базы данных DocumentDB необходимо указать ключ раздела, если коллекция подготовлена с 10 000 (или более) единиц запросов. Ключ раздела также потребуется в будущем, если вы захотите масштабировать пропускную способность коллекции до более 10 000 единиц запросов. Поэтому настоятельно рекомендуется при создании коллекции настроить [ключ раздела](documentdb-partition-data.md) независимо от начальной пропускной способности. Так как ваши данные могут быть разделены между несколькими разделами, выбирайте ключ раздела с большим количеством элементов (от 100 до миллионов уникальных значений), чтобы база данных DocumentDB могла равномерно масштабировать вашу коллекцию и запросы. 

> [!NOTE]
> Ключ раздела представляет собой логическую границу, а не физическую. Поэтому вам не нужно ограничивать количество отдельных значений ключа раздела. Чем больше отдельных значений ключа раздела, тем лучше. Такой ключ предоставляет DocumentDB больше вариантов балансировки нагрузки.

Ниже приведен фрагмент кода для создания коллекции с 3000 единиц запросов в секунду с помощью пакета SDK для .NET.

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

DocumentDB использует модель резервирования пропускной способности. То есть вы получаете счет за *зарезервированную* пропускную способность для коллекции независимо от фактических показателей *использования*. Так как нагрузка, данные и шаблоны использования приложения меняются, вы можете легко увеличивать или уменьшать количество зарезервированных единиц запросов с помощью пакетов SDK для DocumentDB или [портала Azure](https://portal.azure.com).

Каждая коллекция сопоставляется с ресурсом `Offer` в DocumentDB, который содержит метаданные о подготовленной пропускной способности коллекции. Вы можете изменить выделенную пропускную способность. Для этого найдите соответствующий ресурс предложения для коллекции и измените в нем значение пропускной способности. Ниже приведен фрагмент кода для изменения пропускной способности коллекции до 5000 единиц запроса в секунду с помощью пакета SDK для .NET.

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

Изменение пропускной способности не влияет на доступность коллекции. Обычно новая зарезервированная пропускная способность становится доступной для приложения в течение нескольких секунд.

## <a name="specifying-request-unit-capacity-in-api-for-mongodb"></a>Указание количества единиц запроса в API для DocumentDB
API для DocumentDB позволяет указать количество единиц запроса в секунду (ЕЗ в секунду), которое вы хотите зарезервировать для коллекции.

API для MongoDB работает с той же моделью резервирования, основанной на пропускной способности, что и DocumentDB. То есть вы получаете счет за *зарезервированную* пропускную способность для коллекции независимо от фактических показателей *использования*. Так как нагрузка, данные и шаблоны использования приложения меняются, вы можете легко увеличивать или уменьшать количество зарезервированных единиц запроса с помощью [портала Azure](https://portal.azure.com).

Изменение пропускной способности не влияет на доступность коллекции. Обычно новая зарезервированная пропускная способность становится доступной для приложения в течение нескольких секунд.

## <a name="request-unit-considerations"></a>Рекомендации по оценке необходимого количества единиц запросов
При оценке количества единиц запросов, которое нужно зарезервировать для коллекции DocumentDB, важно учитывать следующие рекомендации:

* **Размер документа**. По мере увеличения размера документа увеличивается и число единиц запросов, расходуемых на чтение или запись данных.
* **Число свойств документа**. Если все свойства индексируются, число единиц запросов, расходуемых на запись документа, увеличивается вместе с увеличением количества свойств.
* **Согласованность данных**. При использовании уровней согласованности данных Strong или Bounded Staleness на чтение документов расходуются дополнительные единицы запросов.
* **Индексируемые свойства**. Свойства, которые индексируются по умолчанию, определяются политикой индексирования для каждой коллекции. Вы можете сократить потребление единиц запросов, ограничив количество индексируемых свойств или включив отложенное индексирование.
* **Индексирование документов**. По умолчанию каждый документ индексируется автоматически. Исключение некоторых документов из этого процесса позволяет сэкономить единицы запросов.
* **Шаблоны запросов**. Сложность запроса влияет на количество потребляемых единиц запросов для операции. Количество предикатов и их характер, проекции, количество определяемых пользователем функций и размер набора исходных данных — все это влияет на плату за операции запроса.
* **Использование скриптов**.  Так же как и запросы, хранимые процедуры и триггеры расходуют единицы запросов с учетом сложности выполняемых операций. При разработке приложения проверяйте заголовок со стоимостью в единицах запросов, чтобы лучше понять, как эти ресурсы используются каждой из операций.

## <a name="estimating-throughput-needs"></a>Оценка требований к пропускной способности
Единица запроса — это нормализованная мера стоимости обработки запросов. Одна единица запроса соответствует вычислительной мощности, необходимой для чтения (через ссылку self или идентификатор) документа JSON размером 1 КБ, содержащего 10 уникальных значений свойств (кроме свойств системы). Запрос на создание (вставку), замену или удаление того же документа будет использовать дополнительные вычислительные ресурсы службы и поэтому потребует больше единиц запросов.   

> [!NOTE]
> Базовый план 1 единицы запроса для документа размером 1 КБ соответствует простой команде GET по ссылке self или идентификатору этого документа.
> 
> 

Например, ниже приведена таблица, показывающая количество единиц запросов для подготовки с тремя разными размерами документа (1 КБ, 4 КБ и 64 КБ) и двумя разными уровнями производительности (500 операций чтения в секунду + 100 операций записи в секунду и 500 операций чтения в секунду + 500 операций записи в секунду). Согласованность данных была настроена в сеансе, а политике индексации было присвоено значение "Нет".

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Размер документа</strong></p></td>
            <td valign="top"><p><strong>Операций чтения в секунду</strong></p></td>
            <td valign="top"><p><strong>Операций записи в секунду</strong></p></td>
            <td valign="top"><p><strong>Единиц запросов</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1000 единиц запросов в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3000 единиц запросов в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1,3) + (100 * 7) = 1350 единиц запросов в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1,3) + (500 * 7) = 4150 единиц запросов в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9800 единиц запросов в секунду</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 КБ</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29 000 единиц запросов в секунду</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Использование калькулятора единиц запросов
Чтобы клиенты могли точно оценить требуемую пропускную способность, они могут воспользоваться [веб-калькулятором единиц запроса](https://www.documentdb.com/capacityplanner). Это средство помогает оценить количество требующихся единиц запроса для стандартных операций, включая:

* операции создания документа (запись);
* операции чтения документа;
* операции удаления документа.
* операции обновления документа.

Средство также позволяет оценивать требования к хранилищу данных на основе предоставленных образцов документов.

Использовать это средство просто:

1. Загрузите один или несколько образцов документа JSON.
   
    ![Загрузка документов в калькулятор единиц запросов][2]
2. Чтобы оценить требования к хранилищу данных, укажите общее количество документов, которые вы планируете хранить.
3. Укажите нужное количество операций создания, чтения, обновления и удаления документов (в секунду). Чтобы оценить затраты единиц запросов для операций обновления документа, отправьте копию образца документа (см. шаг 1 выше), который содержит стандартные обновления полей.  Например, если операции обновления документа обычно изменяют свойства lastLogin и userVisits, просто скопируйте образец документа, обновите значения этих двух свойств и отправьте копию документа.
   
    ![Ввод требований к пропускной способности в калькуляторе единиц запросов][3]
4. Нажмите кнопку "Вычислить" и ознакомьтесь с результатами.
   
    ![Результаты калькулятора единиц запросов][4]

> [!NOTE]
> Если размер и количество индексированных свойств для разных типов документа существенно отличаются, отправьте в средство образцы стандартного документа каждого *типа* и проведите вычисления.
> 
> 

### <a name="use-the-documentdb-request-charge-response-header"></a>Использование заголовка ответа на запрос DocumentDB о затратах
Каждый ответ от службы DocumentDB включает пользовательский заголовок (`x-ms-request-charge`), который содержит количество используемых на данный запрос единиц запросов. Этот заголовок также доступен и в пакетах SDK для DocumentDB. В пакете .NET SDK RequestCharge является свойством объекта ResourceResponse.  В обозревателе запросов DocumentDB на портале Azure содержится информация о затратах на выполненные запросы.

![Анализ затрат единиц расходов в обозревателе запросов][1]

Исходя из этого, один из методов оценки необходимого для приложения объема зарезервированной пропускной способности — записать затраты единиц запросов на выполнение стандартных операций для определенного документа, который используется приложением, а затем оценить предполагаемое количество операций в секунду.  Не забудьте учесть стандартные запросы и скрипт DocumentDB, а также выполнить их оценку.

> [!NOTE]
> Если размер и количество индексированных свойств для типов документа существенно отличаются, запишите затраты единиц запросов для соответствующей операции, выполняемой с каждым *типом* стандартного документа.
> 
> 

Например:

1. Запишите затраты единиц запросов на создание (вставку) стандартного документа. 
2. Запишите затраты единиц запросов на чтение стандартного документа.
3. Запишите затраты единиц запросов на обновление стандартного документа.
4. Запишите затраты единиц запросов на выполнение стандартных, общих запросов документа.
5. Запишите затраты единиц запросов на все настраиваемые скрипты (хранимые процедуры, триггеры, определяемые пользователем функции), которые используются приложением.
6. Рассчитайте необходимое количество единиц запросов для операций, которые вы планируете выполнять каждую секунду.

### <a id="GetLastRequestStatistics"></a>Выполнение команды MongoDB GetLastRequestStatistics с помощью API
API для MongoDB поддерживает пользовательскую команду *getLastRequestStatistics*, используемую для получения затрат единиц запроса для указанных операций.

Например, в оболочке Mongo выполните операцию, для которой необходимо проверить затраты единиц запроса.
```
> db.sample.find()
```

Затем выполните команду *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Исходя из этого, один из методов оценки необходимого для приложения объема зарезервированной пропускной способности — записать затраты единиц запросов на выполнение стандартных операций для определенного документа, который используется приложением, а затем оценить предполагаемое количество операций в секунду.

> [!NOTE]
> Если размер и количество индексированных свойств для типов документа существенно отличаются, запишите затраты единиц запросов для соответствующей операции, выполняемой с каждым *типом* стандартного документа.
> 
> 

## <a name="use-api-for-mongodbs-portal-metrics"></a>Использование метрик API для MongoDB, доступных на портале
Самый простой способ получить достоверную оценку затрат единиц запроса для API для базы данных MongoDB — использовать метрики [портала Azure](https://portal.azure.com). Используя диаграммы *Число запросов* и *Запросить оплату*, можно получить приблизительное количество единиц запроса, потребляемое каждой операцией, и количество единиц запроса, которое они потребляют относительно друг друга.

![Метрики API для MongoDB, доступные на портале][6]

## <a name="a-request-unit-estimation-example"></a>Пример оценки количества единиц запросов
Рассмотрим следующий документ размером примерно 1 КБ.

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> В DocumentDB документы минифицированы, поэтому их размер в системе немного меньше 1 КБ.
> 
> 

В следующей таблице показаны приблизительные затраты единиц запросов на выполнение стандартных операций в этом документе (при оценке приблизительных затрат на единицы запросов предполагается, что уровень согласованности для учетной записи имеет значение "Сеанс" и что все документы индексируются автоматически):

| Операция | Затраты единиц запросов |
| --- | --- |
| Создание документа |~ 15 ЕЗ |
| Чтение документа |~ 1 ЕЗ |
| Запрос документа по идентификатору |~ 2,5 ЕЗ |

Кроме того, эта таблица содержит приблизительные затраты единиц запросов для стандартных запросов, используемых в приложении:

| Запрос | Затраты единиц запросов | # Количество возвращенных документов |
| --- | --- | --- |
| Выбор продуктов питания по идентификатору |~ 2,5 ЕЗ |1 |
| Выбор продуктов питания по производителю |~ 7 ЕЗ |7 |
| Выбор группы продуктов питания и сортировка по весу |~ 70 ЕЗ |100 |
| Выбор десяти первых продуктов питания в группе |~ 10 ЕЗ |10 |

> [!NOTE]
> Затраты единиц запросов зависят от количества возвращенных документов.
> 
> 

На основе этой информации можно оценить количество необходимых единиц запросов для приложения в зависимости от предполагаемого количества операций и запросов в секунду:

| Операции и запросы | Предполагаемое количество в секунду | Необходимые единицы запросов |
| --- | --- | --- |
| Создание документа |10 |150 |
| Чтение документа |100 |100 |
| Выбор продуктов питания по производителю |25 |175 |
| Выбор продуктов питания по группе |10 |700 |
| Выбор первых десяти продуктов питания |15 |Всего 150 |

В этом случае ожидается, что для средней пропускной способности необходимо 1,275 единиц запросов в секунду.  Если округлить значение до сотен, получается, что для коллекции этого приложения мы должны подготовить 1300 единиц запросов в секунду.

## <a id="RequestRateTooLarge"></a> Превышение лимита зарезервированной пропускной способности в DocumentDB
Обратите внимание, что удельный расход единиц запросов оценивается в расчете на одну секунду. Для приложений, которые превышают подготовленный показатель единиц запросов для коллекции, будет применяться функция регулирования запросов до тех пор, пока их значение не станет ниже зарезервированного уровня. При регулировании сервер заблаговременно завершит запрос с ошибкой RequestRateTooLargeException (код состояния HTTP: 429) и вернет заголовок x-ms-retry-after-ms, указывая время в миллисекундах, спустя которое можно повторно выполнить запрос.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Если вы используете клиентский пакет SDK для .NET и запросы LINQ, в большинстве случаев вам никогда не придется иметь дело с этим исключением. Текущая версия клиентского пакета SDK для .NET перехватит этот ответ, обработает заголовок "retry-after", указанный сервером, и отправит запрос повторно. Если к вашей учетной записи параллельно имеет доступ только один клиент, следующая попытка будет успешной.

В случае, если к вашей учетной записи имеют доступ несколько клиентов и они выполняют запросы одновременно, процедуры отправки повторного запроса по умолчанию может быть недостаточно, и клиент выдаст для приложения исключение DocumentClientException с кодом состояния 429. В таких случаях рекомендуется обработать алгоритм поведения при отправке повторных запросов и логику для процедуры обработки ошибок приложения или увеличить зарезервированную пропускную способность для коллекции.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Превышение лимита зарезервированной пропускной способности в API для DocumentDB
Для приложений, которые превышают подготовленный объем единиц запроса для коллекции, будет применяться регулирование до тех пор, пока потребление не опустится ниже зарезервированного уровня. В случае регулирования серверная части будет заблаговременно завершать запрос с кодом ошибки *16500* *Слишком много запросов*. По умолчанию API для MongoDB автоматически повторяет запрос до 10 раз, прежде чем возвращать код ошибки *Слишком много запросов*. Если возникает много ошибок *Слишком много запросов*, рекомендуется добавить механизм повтора в процедуры обработки ошибок своего приложения или [увеличить зарезервированную пропускную способность для коллекции](documentdb-set-throughput.md).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о резервировании пропускной способности с помощью баз данных Azure DocumentDB см. в следующих ресурсах:

* [Цены на DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Моделирование данных в DocumentDB](documentdb-modeling-data.md)
* [Уровни производительности в DocumentDB](documentdb-partition-data.md)

Дополнительные сведения о DocumentDB можно найти в [документации](https://azure.microsoft.com/documentation/services/documentdb/)по Azure DocumentDB. 

Инструкции по тестированию масштабирования и производительности с помощью Azure DocumentDB см. в [этой статье](documentdb-performance-testing.md).

[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
[6]: ./media/documentdb-request-units/api-for-mongodb-metrics.png

