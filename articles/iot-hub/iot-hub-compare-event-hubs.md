---
title: "Сравнение Центра Интернета вещей Azure и концентраторов событий Azure | Документация Майкрософт"
description: "Сравнение служб Центра Интернета вещей и концентраторов событий Azure, в котором выделены функциональные различия и случаи использования. Сравниваются поддерживаемые протоколы, функции мониторинга, управления устройствами и передачи файлов."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 2075c7a1b8f3393e100ab92ae7d497c56965f887
ms.lasthandoff: 01/31/2017


---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Сравнение центра Интернета вещей Azure и концентраторов событий Azure
Одним из основных способов использования центра Интернета вещей является сбор телеметрических данных с устройств. По этой причине Центр Интернета вещей часто сравнивается с [концентраторами событий Azure][Azure Event Hubs]. Как и центры IoT, концентраторы событий — это служба обработки событий, используемая для крупномасштабной передачи событий и данных телеметрии в облако при низкой задержке и с высокой надежностью.

Однако между службами имеется множество различий, которые подробно описаны в следующей таблице.

| Область | Центр IoT | Концентраторы событий |
| --- | --- | --- |
| Модели связи | Позволяет [передачу данных с устройства в облако][lnk-d2c-guidance] (обмен сообщениями, передача файлов и сообщаемые свойства) и [передачу данных из облака на устройство][lnk-c2d-guidance] (прямые методы, требуемые свойства, обмен сообщениями). |Обеспечивает только передачу событий (обычно учитывается при передаче данных с устройства в облако). |
| Сведения о состоянии устройства | [Двойники устройств][lnk-twins] могут хранить и запрашивать сведения о состоянии устройства. | Сведения о состоянии устройства не сохраняются. |
| Поддержка протоколов |Поддерживает протоколы MQTT, MQTT через WebSocket, AMQP, AMQP через WebSocket и HTTP. Кроме того, Центр Интернета вещей работает со [шлюзом протокола Azure IoT][lnk-azure-protocol-gateway] — настраиваемой реализацией шлюза протокола для поддержки пользовательских протоколов. |Поддерживает протоколы AMQP, AMQP через WebSocket и HTTP. |
| Безопасность |Обеспечивает идентификацию каждого устройства и контроль доступа с возможностью отзыва. См. [раздел "Безопасность" руководства для разработчиков Центра Интернета вещей]. |Обеспечивает [политики общего доступа][Event Hubs - security], действующие для всего концентратора событий, с ограниченными возможностями отзыва посредством [политик издателя][Event Hubs publisher policies]. От решений IoT часто требуется реализация настраиваемого решения для поддержки учетных данных отдельных устройств и принятие мер против спуфинга. |
| Мониторинг операций |Позволяет решениям IoT подписываться на широкий ряд событий управления удостоверениями и подключения устройств, включая отдельные ошибки проверки подлинности устройств, регулирование и исключения, связанные с недопустимым форматом. С помощью этих событий можно быстро выявлять неполадки на уровне отдельных устройств. |Предоставляются только статистические показатели. |
| Масштаб |Оптимизирован для поддержки миллионов одновременно подключенных устройств. |Поддерживает меньшее число одновременных подключений: до 5000 подключений AMQP (согласно [квотам служебной шины Azure][Azure Service Bus quotas]). С другой стороны, концентраторы событий позволяют пользователям указывать раздел для каждого отправляемого сообщения. |
| Пакеты SDK для устройств |Предоставляет [пакеты SDK для устройств][Azure IoT SDKs] для разнообразных платформ и языков, помимо API MQTT, AMQP и HTTP. |Поддерживается в .NET, Java и языке C. Кроме того, предоставляет интерфейсы передачи AMQP и HTTP. |
| Передача файла |Позволяет решениям IoT передавать файлы из устройств в облако. Включает в себя конечную точку уведомления о файлах для интеграции рабочих процессов и категорию мониторинга операций для поддержки отладки. | Не поддерживается. |
| Маршрутизация сообщений в несколько конечных точек | Поддерживается до 10 пользовательских конечных точек. Правила определяют способ маршрутизации сообщений в пользовательские конечные точки. Дополнительные сведения см. в статье [Отправка и получение сообщений в Центре Интернета вещей][lnk-devguide-messaging]. | Для диспетчеризации сообщений требуется запись и размещение дополнительного кода. |

В заключение скажем, что, даже если единственным вариантом использования является передача телеметрических данных от устройства в облако, Центр Интернета вещей предоставляет службу, разработанную для подключения устройств Интернета вещей. Для Центра Интернета вещей будут и дальше появляться новые возможности, ориентированные на такие сценарии. Концентраторы событий предназначены для приема огромных объемов событий как в контексте обмена данными между разными ЦОД, так и в пределах одного ЦОД.

Использование Центра Интернета вещей вместе с концентраторами событий не является экзотикой. В таких случаях Центр Интернета вещей обрабатывает передачу данных с устройств в облако, а концентраторы событий — дальнейшую передачу событий в модули обработки в реальном времени.

### <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о планировании развертывания Центра Интернета вещей см. в статье [Масштабирование Центра Интернета вещей][lnk-scaling].

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей][lnk-devguide]
* [Пакет SDK для шлюза IoT (бета-версия): отправка сообщений с устройства в облако через виртуальное устройство с помощью Linux][lnk-gateway]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[раздел "Безопасность" руководства для разработчиков Центра Интернета вещей]: iot-hub-devguide-security.md.
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-what-is-event-hubs.md#event-publishers
[Azure Service Bus quotas]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md

