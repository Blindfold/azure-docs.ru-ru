---
title: "Пошаговое руководство по предварительно настроенному решению удаленного мониторинга | Документация Майкрософт"
description: "Описание предварительно настроенного решения удаленного мониторинга Azure IoT и его архитектура."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: dc8ee6a0f17c20c5255d95c7b6f636d89ffe3aee
ms.openlocfilehash: 9bd4232670256ec7889dd367ea2ea01a2845e789
ms.lasthandoff: 02/27/2017


---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Пошаговое руководство по работе с настроенным решением для удаленного мониторинга
## <a name="introduction"></a>Введение
[Предварительно настроенное решение][lnk-preconfigured-solutions] удаленного мониторинга набора IoT Suite — это реализация решения комплексного мониторинга для сценария с использованием нескольких компьютеров в удаленных расположениях. Это решение объединяет основные службы Azure, чтобы обеспечить универсальную реализацию бизнес-сценария. Его можно использовать в качестве отправной точки для собственной реализации и [настроить][lnk-customize] в соответствии с потребностями конкретной организации.

В этой статье рассматриваются некоторые основные компоненты решения для удаленного мониторинга, чтобы вы смогли представить, как оно работает. Эти знания помогут вам:

* устранить проблемы, возникшие с решением;
* спланировать настройку решения в соответствии с определенными требованиями; 
* спроектировать собственное решение IoT, использующее службы Azure.

## <a name="logical-architecture"></a>Логическая архитектура
На следующей диаграмме показаны логические компоненты предварительно настроенного решения.

![Логическая архитектура](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Виртуальные устройства
В настроенном решении виртуальное устройство представляет собой устройство охлаждения (например, кондиционер воздуха в помещении или установку кондиционирования воздуха объекта). При развертывании предварительно настроенного решения вы также автоматически подготавливаете четыре имитации устройств, которые выполняются в [веб-задании Azure][lnk-webjobs]. Эти устройства упрощают изучение поведения решения, не требуя развертывания физических устройств. Сведения о развертывании физического устройства см. в статье [Подключение устройства к предварительно настроенному решению для удаленного мониторинга (Windows)][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Отправка сообщений с устройства в облако
Каждое виртуальное устройство может отправлять следующие типы сообщений в центр IoT.

| Сообщение | Описание |
| --- | --- |
| Запуск |При запуске устройство отправляет в серверную часть сообщение со **сведениями об устройстве** . Например, идентификатор устройства и список команд и методов, поддерживаемых устройством. |
| Присутствие |Устройство периодически отправляет сообщение о **присутствии** , чтобы сообщить, может ли оно определить присутствие датчика. |
| Телеметрия |Устройство периодически отправляет сообщение **телеметрии** со значениями температуры и влажности, полученными от виртуальных датчиков. |

> [!NOTE]
> Решение сохраняет список команд, поддерживаемых устройством, в базе данных DocumentDB, а не в двойнике устройства.
> 
> 

### <a name="properties-and-device-twins"></a>Свойства и двойники устройств
Имитации устройств отправляют следующие свойства устройств [двойнику][lnk-device-twins] в Центре Интернета вещей в виде *сообщаемых свойств*. Устройство отправляет сообщаемые свойства при запуске и в ответ на метод или команду **изменения состояния устройства**.

| Свойство | Назначение |
| --- | --- |
| Config.TelemetryInterval | Частота (в секундах), с которой устройство отправляет данные телеметрии |
| Config.TemperatureMeanValue | Указывает среднее значение имитированных данных телеметрии (температуры) |
| Device.DeviceID |Идентификатор, предоставленный или назначенный при создании устройства в решении |
| Device.DeviceState | Состояние, сообщаемое устройством |
| Device.CreatedTime |Время создания устройства в решении |
| Device.StartupTime |Время запуска устройства |
| Device.LastDesiredPropertyChange |Номер версии последнего изменения требуемых свойств |
| Device.Location.Latitude |Широта расположения устройства |
| Device.Location.Longitude |Долгота расположения устройства |
| System.Manufacturer |Производитель устройства |
| System.ModelNumber |Номер модели устройства |
| System.SerialNumber |Серийный номер устройства |
| System.FirmwareVersion |Текущая версия встроенного ПО на устройстве |
| System.Platform |Архитектура платформы устройства |
| System.Processor |Процессор, управляющий устройством |
| System.InstalledRAM |Объем ОЗУ, установленного на устройстве |

Симулятор заполняет эти свойства в виртуальных устройствах образцами данных. Каждый раз, когда симулятор инициализирует имитацию устройства, устройство отправляет предварительно определенные метаданные в Центр Интернета вещей в виде сообщаемых свойств. Сообщаемые свойства могут обновляться только устройством. Чтобы изменить сообщаемое свойство, задайте на портале решения требуемое свойство. Устройство отвечает за следующие действия:
1. Периодическое получение требуемых свойств из Центра Интернета вещей.
2. Обновление своей конфигурации с помощью значения требуемого свойства.
3. Отправка нового значения обратно в центр в виде сообщаемого свойства.

На панели мониторинга решения можно воспользоваться *требуемыми свойствами*, чтобы задать значения свойств устройства с помощью [двойника устройства][lnk-device-twins]. Как правило, устройство считывает в центре значение требуемого свойства для обновления своего внутреннего состояния и сообщает об изменении в виде сообщаемого свойства.

> [!NOTE]
> Код имитации устройства использует только требуемые свойства **Desired.Config.TemperatureMeanValue** и **Desired.Config.TelemetryInterval**, чтобы обновить сообщаемые свойства, отправляемые обратно в Центр Интернета вещей. Все прочие запросы на изменение требуемых свойств в имитации устройства игнорируются.

### <a name="methods"></a>Методы
Имитации устройств могут обрабатывать следующие методы ([прямые методы][lnk-direct-methods]), вызываемые из портала решения через Центр Интернета вещей:

| Метод | Описание |
| --- | --- |
| InitiateFirmwareUpdate |Сообщает устройству о выполнении обновления встроенного ПО |
| Reboot |Сообщает устройству о перезагрузке |
| FactoryReset |Сообщает устройству о выполнении сброса до заводских настроек |

Некоторые методы используют сообщаемые свойства для продолжения процесса. Например, метод **InitiateFirmwareUpdate** имитирует на устройстве асинхронное выполнение обновления. Метод немедленно возвращается на устройстве, в то время как асинхронная задача продолжает отправлять обновления состояния обратно на панель мониторинга решения, используя сообщаемые свойства.

### <a name="commands"></a>Команды 
Имитации устройств могут обрабатывать следующие команды (сообщения, отправляемые из облака на устройство), отправляемые из портала решения через Центр Интернета вещей:

| Команда | Описание |
| --- | --- |
| PingDevice |Отправляет на устройство *ping-запрос* для проверки его активности |
| StartTelemetry |Запускает отправку данных телеметрии устройством |
| StopTelemetry |Останавливает отправку данных телеметрии устройством |
| ChangeSetPointTemp |Изменяет значение заданной точки, вокруг которой формируются случайные данные |
| DiagnosticTelemetry |Вызывает отправку симулятором устройства дополнительного значения телеметрии (externalTemp) |
| ChangeDeviceState |Изменяет свойство расширенного состояния устройства и отправляет с устройства сообщение со сведениями об устройстве |

> [!NOTE]
> Сравнение этих команд (сообщений, отправляемых из облака на устройство) и методов (прямых методов) см. в статье [Руководство по обмену данными между облаком и устройством][lnk-c2d-guidance].
> 
> 

## <a name="iot-hub"></a>Центр IoT
[Центр Интернета вещей][lnk-iothub] принимает данные, отправленные с устройств в облако, и предоставляет к ним доступ заданиям Azure Stream Analytics (ASA). Каждый поток заданий ASA использует отдельную группу потребителей центра IoT для считывания потока сообщений с устройств.

Центр Интернета вещей также выполняет в решении следующие функции:

- Поддерживает реестр удостоверений, в котором хранятся идентификаторы и ключи аутентификации всех устройств с разрешением на подключение к порталу. С помощью реестра удостоверений можно включать и отключать устройства.
- Отправляет устройствам команды от имени портала устройства.
- Вызывает на устройствах методы от имени портала устройства.
- Поддерживает двойники устройств для всех зарегистрированных устройств. Двойник устройства хранит значения свойств, сообщаемые устройством. Двойник устройства также хранит требуемые свойства, заданные на портале решения, которые извлекаются устройством при следующем подключении.
- Планирует задания, чтобы задать значения свойств для нескольких устройств или вызвать методы на нескольких устройствах.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
В решении удаленного мониторинга [Azure Stream Analytics][lnk-asa] (ASA) отправляет сообщения, полученные с устройств в Центре Интернета вещей, другим компонентам серверной части для обработки или хранения. Каждое задание ASA выполняет определенные функции в зависимости от содержимого сообщений.

**Задание 1. Сведения об устройстве.** Фильтрует сообщения со сведениями об устройстве из входящего потока сообщений и отправляет их в конечную точку концентратора событий. Устройство отправляет сообщения с информацией об устройстве при запуске и в ответ на команду **SendDeviceInfo**. Чтобы определить сообщения со **сведениями об устройстве**, задание использует следующее определение запроса.

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Это задание отправляет выходные данные в концентратор событий для дальнейшей обработки.

**Задание 2. Правила.** Оценивает входящие телеметрические значения температуры и влажности и сравнивает с пороговым значением для устройства. Пороговые значения задаются в редакторе правил, доступном на портале решения. Каждая пара "устройство/значение" хранится с меткой времени в большом двоичном объекте, который обработчик Stream Analytics считывает как **ссылочные данные**. Задание сравнивает все непустые значения с заданным пороговым значением для устройства. Если значение превышает условие ">", задание выводит событие **оповещения**, которое сигнализирует, что пороговое значение превышено, и указывает устройство, значение и метку времени. Чтобы определить сообщения телеметрии, которые должны активировать оповещение, задание использует следующее определение запроса:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Задание отправляет выходные данные в концентратор событий для дальнейшей обработки и сохраняет сведения о каждом оповещении в хранилище BLOB-объектов, откуда их может считывать портал решения.

**Задание 3. Телеметрия.** Работает на входящем потоке телеметрии устройства, используя два метода. Первый отправляет все сообщения телеметрии с устройств в постоянное хранилище BLOB-объектов для долгосрочного хранения. Второй вычисляет среднее, минимальное и максимальное значения влажности в течение пятиминутного скользящего окна, а потом отправляет эти данные в хранилище BLOB-объектов. Портал решения считывает данные телеметрии из хранилища BLOB-объектов, чтобы заполнить диаграммы. Это задание использует следующее определение запроса:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Концентраторы событий
Задания ASA **Сведения об устройстве** и **Правила** выводят данные в концентраторы событий, чтобы надежно перенаправить их в **обработчик событий**, запущенный в веб-задании.

## <a name="azure-storage"></a>Хранилище Azure
Решение использует хранилище BLOB-объектов Azure, чтобы сохранять все необработанные и сводные данные телеметрии с устройств в решении. Портал считывает данные телеметрии из хранилища BLOB-объектов, чтобы заполнить диаграммы. Чтобы отобразить оповещения, портал решения считывает данные из хранилища BLOB-объектов, регистрирующего события превышения настроенных пороговых значений телеметрии. Решение также использует хранилище BLOB-объектов для записи пороговых значений, которые пользователь задал на портале решения.

## <a name="webjobs"></a>Веб-задания
В дополнение к размещению имитаций устройств в веб-заданиях решения также размещается **обработчик событий**, работающий в веб-задании Azure, который обрабатывает ответы команд. Он использует сообщения об ответе на команды для обновления журнала команд устройства (хранится в базе данных DocumentDB).

## <a name="documentdb"></a>DocumentDB
Решение использует базу данных DocumentDB для хранения сведений о подключенных к нему устройствах, например журнала команд, отправленных на устройства с портала решения, и методов, вызванных с портала решения.

## <a name="solution-portal"></a>Портал решения

Портал решения представляет собой веб-приложение, разворачиваемое в составе предварительно настроенного решения. Основными страницами на портале решения являются панель мониторинга и список устройств.

### <a name="dashboard"></a>Панель мониторинга
Эта страница в веб-приложении использует элементы управления JavaScript PowerBI (см. в [репозитории визуальных элементов PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) для визуализации данных телеметрии с устройств. Решение использует задание телеметрии ASA для записи данных телеметрии в хранилище BLOB-объектов.

### <a name="device-list"></a>Список устройств
На этой странице портала решения можно выполнить перечисленные ниже действия.

* Подготовка нового устройства. Это действие задает уникальный идентификатор устройства и создает ключ проверки подлинности. Сведения об устройстве записываются в реестр удостоверений центра IoT и базу данных DocumentDB определенного решения.
* Управление свойствами устройства. Это действие выполняет просмотр имеющихся свойств и их обновление.
* Отправка команд на устройство.
* Просмотр журнала команд для устройства.
* Включение и отключение устройств.

## <a name="next-steps"></a>Дальнейшие действия
В записях блога на сайте TechNet приведены дополнительные сведения о предварительно настроенном решении удаленного мониторинга:

* [IoT Suite - Under The Hood - Remote Monitoring (IoT Suite. Как работает удаленный мониторинг).](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices (IoT Suite. Удаленный мониторинг: добавление реальных и виртуальных устройств).](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Дополнительные сведения об IoT Suite см. в следующих статьях.

* [Подключение устройства к предварительно настроенному решению для удаленного мониторинга][lnk-connect-rm]
* [Разрешения на сайте azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
