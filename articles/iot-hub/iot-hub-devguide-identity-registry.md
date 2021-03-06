---
title: "Общие сведения о реестре удостоверений Центра Интернета вещей Azure | Документация Майкрософт"
description: "Руководство разработчика. Описание реестра удостоверений Центра Интернета вещей, а также сведения о том, как с его помощью управлять своими устройствами. Содержит сведения об импорте и экспорте удостоверений устройств в пакетном режиме."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 75a2fa16a7e33cf85746538e120ca90a389b05c5
ms.lasthandoff: 03/24/2017


---
# <a name="understand-identity-registry-in-your-iot-hub"></a>Общие сведения о реестре удостоверений в Центре Интернета вещей

## <a name="overview"></a>Обзор

Каждый Центр Интернета вещей имеет реестр удостоверений, в котором содержатся сведения об устройствах с разрешением на подключение к Центру Интернета вещей. Перед подключением устройства к Центру Интернета вещей в реестр удостоверений нужно добавить запись об этом устройстве. Устройство также должно пройти аутентификацию в Центре Интернета вещей на основе учетных данных, хранящихся в реестре удостоверений.

На высоком уровне реестр удостоверений является коллекцией с поддержкой REST, состоящей из ресурсов удостоверений устройств. Добавляя записи в реестр удостоверений, Центр Интернета вещей создает в службе уникальные ресурсы устройства (например, очередь с сообщениями, отправленными из облака на устройство).

### <a name="when-to-use"></a>Сценарии использования

Реестр удостоверений можно использовать для подготовки устройств, подключаемых к Центру Интернета вещей, а также для управления доступом устройств к конечным точкам центра, доступным с устройства.

> [!NOTE]
> Реестр удостоверений не содержит метаданные конкретного приложения.

## <a name="identity-registry-operations"></a>операции с реестром удостоверений.

Реестр удостоверений Центра Интернета вещей поддерживает такие операции:

* создание удостоверения устройства;
* обновление удостоверения устройства;
* получение удостоверения устройства по идентификатору;
* удаление удостоверения устройства;
* отображение списка, содержащего до 1000 удостоверений;
* экспорт всех удостоверений в хранилище BLOB-объектов Azure;
* импорт всех удостоверений из хранилища BLOB-объектов Azure.

Все эти операции могут использовать оптимистичный параллелизм (согласно [RFC7232][lnk-rfc7232]).

> [!IMPORTANT]
> Единственный способ получить все удостоверения, которые содержит реестр удостоверений Центра Интернета вещей, — использовать функцию [Экспорт][lnk-export].

Реестр удостоверений Центра Интернета вещей:

* не содержит метаданные приложения;
* доступен как словарь (в качестве ключа используется свойство **deviceId** );
* не поддерживает выразительные запросы.

Решение IoT обычно включает в себя отдельное хранилище, содержащее метаданные приложений. Например, в хранилище решения для интеллектуального здания будут записываться данные о комнате, где установлен датчик температуры.

> [!IMPORTANT]
> Реестр удостоверений следует использовать только для управления устройствами и операций подготовки. Выполнение операций в реестре удостоверений не должно влиять на пропускную способность операций в среде выполнения. Например, проверка состояния подключения устройства перед отправкой команды не является поддерживаемым шаблоном. Проверьте [уровни регулирования][lnk-quotas] реестра удостоверений и шаблон [пульса устройств][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Отключение устройств

Чтобы отключить устройства, обновите в реестре свойство **status** удостоверения. Обычно это свойство используется в двух сценариях.

* В процессе оркестрации подготовки. Дополнительные сведения см. в разделе [Подготовка устройств][lnk-guidance-provisioning].
* Если по какой-либо причине вы решите, что безопасность устройства нарушена или оно является неавторизованным.

## <a name="import-and-export-device-identities"></a>Импорт и экспорт удостоверений устройств

Можно выполнить массовый экспорт удостоверений устройств из реестра удостоверений Центра Интернета вещей, используя асинхронные операции в [конечной точке поставщика ресурсов Центра Интернета вещей][lnk-endpoints]. Экспорт — это долгосрочное задание, использующее предоставленный клиентом контейнер больших двоичных объектов, необходимых для сохранения идентификационных данных устройств, прочитанных из реестра удостоверений устройств.

Можно выполнить массовый импорт удостоверений устройств в реестр удостоверений Центра Интернета вещей, используя асинхронные операции в [конечной точке поставщика ресурсов Центра Интернета вещей][lnk-endpoints]. Импорт — это долгосрочное задание, использующее данные в предоставленном клиентом контейнере больших двоичных объектов, необходимых для записи идентификационных данных об устройстве в реестр удостоверений.

* Дополнительные сведения об интерфейсах API импорта и экспорта см. в статье [IoT Hub resource provider REST APIs][lnk-resource-provider-apis] (Интерфейсы REST API поставщика ресурсов Центра Интернета вещей).
* Дополнительные сведения о выполнении заданий импорта и экспорта см. в статье [Массовое управление удостоверениями устройств центра IoT][lnk-bulk-identity].

## <a name="device-provisioning"></a>Подготовка устройств

Данные устройства, которые хранятся в заданном решении IoT, зависят от конкретных требований этого решения. Но в решении как минимум должны сохраняться удостоверения устройства и ключи проверки подлинности. Центр Интернета вещей Azure имеет реестр удостоверений, который может хранить значения для каждого устройства, например идентификаторы, ключи проверки подлинности и коды состояния. Для хранения любых дополнительных данных устройств решение может использовать другие службы Azure, такие как хранилище таблиц Azure, хранилище BLOB-объектов Azure или Azure DocumentDB.

*Подготовка устройств* — это процесс добавления исходных данных устройства в хранилища в решении. Чтобы предоставить новому устройству доступ к центру, необходимо добавить идентификатор и ключи устройства в реестр удостоверений Центра Интернета вещей. В рамках процесса подготовки может потребоваться инициализировать данные устройства в других хранилищах решения.

## <a name="device-heartbeat"></a>Пульс устройства

Реестр удостоверений Центра Интернета вещей содержит поле **connectionState**. Поле **connectionState** используется только во время разработки и отладки. Во время выполнения решения Интернета вещей не должны запрашивать это поле (например, чтобы проверить подключение устройства и сделать выбор между сообщением из облака на устройство и SMS).

Если решению Интернета вещей необходимо определять, подключено ли устройство (во время выполнения либо с более высокой точностью, чем позволяет свойство **connectionState**), в него необходимо внедрить *шаблон пульса*.

При использовании шаблона пульса устройство отправляет сообщения с устройства в облака с заданной периодичностью (например не реже, чем каждый час). Если отправка данных при этом не требуется, устройство отправляет с устройства в облака пустое сообщение (обычно содержащее свойство, идентифицирующее его как пульс). На стороне службы решение хранит карту последнего пульса, полученного по каждому устройству, и сообщает о проблеме, если устройство не получает сообщение пульса в ожидаемое время.

Более сложные варианты могут включать в себя данные [мониторинга операций][lnk-devguide-opmon], позволяющие определять, какие устройства пытаются, но не могут установить подключение. Внедряя в решение шаблон пульса, учитывайте [квоты и ограничения Центра Интернета вещей][lnk-quotas].

> [!NOTE]
> Если состояние подключения устройства нужно решению Интернета вещей исключительно для того, чтобы определить, отправлять ли сообщения из облака на устройство, и сообщения не вещаются на большие наборы устройств, то следует рассмотреть заметно более простой способ — использовать небольшой срок действия. Это позволяет добиться того же результата, что и при обслуживании реестра состояний подключения устройств, действующего по принципу пульса, но с большей эффективностью. Запрашивая подтверждения сообщений у центра IoT, также можно узнать, какие устройства могут получать сообщения, а какие находятся не в сети или в неработоспособны.

## <a name="reference-topics"></a>Справочные материалы

В следующих справочных материалах предоставлены дополнительные сведения о реестре удостоверений.

## <a name="device-identity-properties"></a>Свойства удостоверений устройств

Удостоверения устройств отображаются как JSON-документы с нижеуказанными свойствами.

| Свойство | Параметры | Описание |
| --- | --- | --- |
| deviceId |Обязательно, при обновлениях доступно только для чтения |Строка с учетом регистра (длиной до 128 символов), состоящая из букв и цифр в 7-битовом формате ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId |Обязательно, только для чтения |Созданная Центром Интернета вещей строка с учетом регистра (длиной до 128 знаков). Это значение позволяет различать устройства с одинаковым свойством **deviceId**, которые были удалены, а затем созданы повторно. |
| etag |Обязательно, только для чтения |Строка, выполняющая функцию ненадежного заголовка ETag для удостоверения устройства (согласно [RFC7232][lnk-rfc7232]). |
| auth |необязательный |Составной объект, содержащий сведения об аутентификации и материалы безопасности. |
| auth.symkey |необязательный |Составной объект, содержащий первичный и вторичный ключи, хранящиеся в формате base64. |
| status |обязательно |Индикатор доступа. Доступно два значения: **Включено** или **Отключено**. Если указано значение **Включено**, устройство может подключаться. Если указано значение **Отключено**, устройство не может подключаться ни к одной конечной точке, обращенной к устройству. |
| statusReason |необязательный |Строка длиной 128 знаков, указывающая причину, по которой выбран тот или иной статус удостоверения устройства. Разрешены все символы UTF-8. |
| statusUpdateTime |Только для чтения |Временной индикатор, показывающий дату и время последнего обновления статуса. |
| connectionState |Только для чтения |Поле, отображающее состояние подключения: **Подключено** или **Отключено**. Это поле отображает состояние подключения устройства для центра IoT. **Важно!**Используйте это поле только в целях разработки и отладки. Состояние подключения обновляется только для устройств, использующих протокол MQTT или AMQP. Кроме того, оно определяется по запросам проверки связи (по протоколу MQTT или AMQP) и может иметь максимальную задержку равную всего лишь 5 минутам. В связи с этим могут возникать ложно положительные результаты, например, когда отключенные устройства отображаются как подключенные. |
| connectionStateUpdatedTime |Только для чтения |Временной индикатор, показывающий дату и время последнего обновления состояния подключения. |
| lastActivityTime |Только для чтения |Временной индикатор, показывающий дату и время последнего подключения устройства, получения сообщения на устройство и отправки сообщения с него. |

> [!NOTE]
> Статус подключения отображает статус подключения для центра IoT. При некоторых состояниях и конфигурациях сети обновления этого состояния могут задерживаться.

## <a name="additional-reference-material"></a>Дополнительные справочные материалы

Другие справочные статьи в руководстве разработчика для Центра Интернета вещей:

* Статья [IoT Hub endpoints][lnk-endpoints] (Конечные точки Центра Интернета вещей) содержит сведения о конечных точках, которые каждый Центр Интернета вещей предоставляет для операций управления и среды выполнения.
* Статья [Throttling and quotas][lnk-quotas] (Регулирование и квоты) содержит сведения о квотах, применимых к службе Центра Интернета вещей, и ожидаемом поведении регулирования при использовании службы.
* В статье [Пакеты SDK для устройств и служб Интернета вещей Azure][lnk-sdks] указаны различные языковые пакеты SDK, которые можно использовать при разработке приложений для устройств и служб, взаимодействующих с Центром Интернета вещей.
* В статье [Справочник по языку запросов для двойников и заданий][lnk-query] описывается язык запросов, который можно использовать для получения сведений о двойниках устройств и заданиях из Центра Интернета вещей.
* Статья [Поддержка MQTT в центре IoT][lnk-devguide-mqtt] содержит дополнительные сведения о поддержке протокола MQTT в Центре Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать реестр удостоверений Центра Интернета вещей, вас могут заинтересовать следующие статьи в руководстве разработчика для Центра Интернета вещей:

* [Управление доступом к Центру Интернета вещей][lnk-devguide-security]
* [Основные сведения о двойниках устройств (предварительная версия)][lnk-devguide-device-twins]
* [Вызов прямого метода на устройстве (предварительная версия)][lnk-devguide-directmethods]
* [Schedule jobs on multiple devices][lnk-devguide-jobs] (Планирование заданий на нескольких устройствах)

Если вы хотели бы применить на практике некоторые основные понятия, описанные в этой статье, можно просмотреть следующее руководство по Центру Интернета вещей:

* [Приступая к работе с Центром Интернета вещей Azure][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md

