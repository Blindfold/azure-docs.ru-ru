---
title: "Обзор Центра Интернета вещей Azure | Документация Майкрософт"
description: "Общие сведения о службе Центра Интернета вещей Azure: что такое Центр Интернета вещей, подключение устройств, модели обмена данными Интернета вещей, включая обмен данными с использованием службы, шлюзы."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: ed204c466c5cfb60e5ba250b9dacb2524ca384eb
ms.lasthandoff: 04/18/2017


---
# <a name="overview-of-the-azure-iot-hub-service"></a>Общие сведения о службе Центра Интернета вещей Azure
Добро пожаловать в центр IoT Azure. В этой статье представлен обзор Центра Интернета вещей Azure и содержатся сведения о том, как вам может пригодиться эта служба при внедрении решения Интернета вещей. Центр IoT Azure — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. Центр IoT Azure обеспечивает:

* несколько вариантов взаимодействия между устройствами и облаком, в том числе одностороннюю передачу сообщений, передачу файлов и методы "запрос — ответ";
* встроенную декларативную маршрутизацию сообщений к другим службам Azure;
* хранилище для метаданных устройств и информации о состоянии синхронизации с возможностью запрашивания данных;
* безопасную связь и контроль доступа с использованием отдельных ключей безопасности или сертификатов X.509 для каждого устройства;
* комплексный мониторинг взаимодействия, а также событий управления идентификацией устройств;
* простое подключение устройств, так как используются библиотеки устройств для большинства популярных языков и платформ.

В статье [Сравнение центра IoT и концентраторов событий][lnk-compare] описываются ключевые различия между этими двумя службами и важные преимущества использования Центра Интернета вещей в соответствующих решениях.

В статье [Комплексная защита в Интернете вещей][lnk-security-ground-up] вы найдете сведения о том, как Azure и Центр Интернета вещей помогут защитить ваше решение.

![Использование центра IoT Azure в качестве облачного шлюза для решения IoT][img-architecture]

> [!NOTE]
> Подробное рассмотрение архитектуры Центра Интернета вещей см. в документе [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Эталонная архитектура Microsoft Azure IoT).
> 
> 

## <a name="iot-device-connectivity-challenges"></a>Трудности, связанные с взаимодействием устройств IoT
Центр IoT и библиотеки позволяют обеспечить надежное и безопасное подключение устройств к серверной части решения. Устройства IoT:

* часто представляют собой встраиваемые системы, у которых отсутствует оператор;
* могут располагаться в удаленных расположениях, получить физический доступ к которым дорого;
* могут быть доступны только через серверную часть решения;
* могут иметь ограниченную мощность и (или) объем ресурсов для обработки;
* могут иметь временное, медленное или дорогостоящее подключение к сети;
* могут требовать использования закрытых, пользовательских или отраслевых протоколов приложения;
* могут создаваться с помощью большого числа популярных аппаратных и программных платформ.

В дополнение к изложенным выше требованиям любое решение IoT также должно быть масштабируемым, безопасным и надежным. В итоге формируется набор требований к подключению, выполнить которые с помощью традиционных технологий, таких как веб-контейнеры и брокеры сообщений, очень трудно.

## <a name="why-use-azure-iot-hub"></a>Почему нужно использовать центр IoT Azure
Помимо богатого выбора способов передачи информации [с устройства в облако][lnk-d2c-guidance] и [из облака на устройство][lnk-c2d-guidance], таких как обмен сообщениями, передача файлов и методы "запрос — ответ", Центр Интернета вещей Azure предлагает следующие средства для решения проблем взаимодействия с устройствами.

* **Двойники устройств**. [Двойники устройств][lnk-twins] позволяют хранить, синхронизировать и получать по запросу метаданные и информацию о состоянии устройства. Двойники устройств — это документы JSON, хранящие сведения о состоянии устройства (метаданные, конфигурации и условия). Центр Интернета вещей сохраняет двойник устройства для каждого устройства, подключаемого к Центру Интернета вещей. 
* **Проверка подлинности для каждого устройства и безопасное подключение**. Вы можете подготовить для каждого устройства собственный [ключ безопасности][lnk-devguide-security] для подключения к Центру Интернета вещей. [Реестр удостоверений Центра Интернета вещей][lnk-devguide-identityregistry] хранит удостоверения устройств и ключи, используемые в решении. Серверная часть решения приложения может добавлять отдельные устройства в список разрешений или запрещающий список, тем самым полностью контролируя доступ к устройствам.
* **Направление сообщений, передаваемых с устройства в облако, службам Azure на основе декларативных правил.** Центр Интернета вещей позволяет определять маршруты сообщений на основе правил маршрутизации, чтобы контролировать, куда будут отправляться сообщения, передаваемые с устройства в облако. Для использования правил маршрутизации не требуется дополнительный программный код. Их можно использовать вместо пользовательских диспетчеров получения и обработки сообщений.
* **Мониторинг операций подключения устройств**. Вы можете получать подробные журналы операций управления идентификацией устройств и событий подключения устройства. С помощью этой возможности мониторинга решение IoT сможет выявлять проблемы подключения, например случаи, когда устройство пытается установить подключение, используя неверные учетные данные, отправляет сообщения слишком часто или отклоняет все сообщения, отправленные из облака на устройство.
* **Обширный набор библиотек устройств**. Существуют и поддерживаются [пакеты SDK Azure для устройств Интернета вещей][lnk-device-sdks] для разных языков и платформ, включая C для многих дистрибутивов Linux, Windows и операционных систем реального времени. Кроме того, пакеты SDK для устройств IoT Azure поддерживают управляемые языки, такие как C#, Java и JavaScript.
* **Протоколы IoT и расширяемость**. Если в вашем решении нельзя использовать библиотеки устройств, центр IoT Azure предоставляет открытый протокол, который позволяет устройствам использовать протоколы MQTT 3.1.1, HTTP 1.1 и AMQP 1.0. Кроме того, в центре IoT можно настроить поддержку пользовательских протоколов. Для этого нужно:
  
  * создать полевой шлюз с помощью [пакета SDK для шлюза Интернета вещей Azure][lnk-gateway-sdk], который преобразует пользовательский протокол в один из трех протоколов, поддерживаемых Центром Интернета вещей. 
  * настроить [шлюз протокола Интернета вещей Azure][protocol-gateway] (компонент с открытым исходным кодом, который выполняется в облаке).
* **Масштабирование**. Центр IoT Azure масштабируется до нескольких миллионов одновременно подключенных устройств и нескольких миллионов событий в секунду.

## <a name="gateways"></a>Шлюзы
В качестве шлюза в решении Интернета вещей обычно используется [шлюз протокола][lnk-gateway], развертываемый в облаке, или [полевой шлюз][lnk-field-gateway], развертываемый локально вместе с устройствами. Шлюз протокола выполняет преобразование протоколов, например MQTT в AMQP. Полевой шлюз может выполнять аналитику на границе, принимать срочные решения, позволяющие уменьшить задержку, предоставлять службы управления устройствами, применять ограничения для безопасности и конфиденциальности, а также преобразовывать протоколы. Оба типа шлюза выступают в качестве посредников между устройствами и центром IoT.

Полевой шлюз отличается от обычного маршрутизатора трафика (например, устройства преобразования сетевых адресов или брандмауэра) тем, что ему отведена активная роль в управлении доступом и потоком информации в решении.

Решение может включать в себя шлюзы протоколов и полевые шлюзы.

## <a name="how-does-iot-hub-work"></a>Принципы работы центра IoT
Центр Интернета вещей Azure реализует шаблон [взаимодействия с поддержкой службы][lnk-service-assisted-pattern] и выступает в роли посредника во взаимодействии между устройствами и серверной частью решения. Задача взаимодействия с поддержкой службы заключается в установлении путей доверенного двунаправленного взаимодействия между системой управления, такой как центр IoT, и специализированными устройствами, которые разворачиваются в недоверенном физическом пространстве. Данный шаблон устанавливает следующие принципы.

* Безопасность имеет приоритет над всеми другими возможностями.
* Устройства не принимают сетевой информации, которую они не запрашивали. Устройство устанавливает все подключения и маршруты только в исходящем режиме. Чтобы устройство получило команду от серверной части решения, оно должно регулярно инициировать подключение для проверки любых команд, ожидающих обработки.
* Устройства должны подключаться и устанавливать маршруты только с общеизвестными службами, которые являются их партнерами, такими как центр IoT.
* Пути взаимодействия между устройством и службой и устройством и шлюзом защищены на уровне протокола приложения.
* Авторизация и проверка подлинности системного уровня основаны на удостоверениях устройств. Они позволяют практически мгновенно отзывать учетные данные и разрешения для доступа.
* Двунаправленное взаимодействие для устройств, которые подключаются нерегулярно из-за проблем с питанием или подключением, упрощается путем хранения команд и уведомлений для устройства до того момента, пока оно не подключится для их получения. Центр IoT хранит отправляемые команды в соответствующих очередях для каждого устройства.
* Полезные данные приложения можно защитить отдельно для безопасной передачи через шлюзы к конкретной службе.

Шаблон взаимодействия с поддержкой службы широко используется в отрасли мобильных устройств для реализации служб push-уведомлений, таких как [службы push-уведомлений Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] и [служба push-уведомлений Apple][lnk-apple-push].

Центр Интернета вещей поддерживается по пути общедоступного пиринга ExpressRoute.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о том, как отправлять сообщения с устройства и получать их из Центра Интернета вещей, а также как настраивать маршруты сообщений, см. в статье [Отправка и получение сообщений в Центре Интернета вещей][lnk-send-messages].

Сведения о том, как в Центре Интернета вещей реализовано стандартизированное управление устройствами для их удаленного администрирования, настройки и обновления, см. в статье [Общие сведения об управлении устройствами с помощью Центра Интернета вещей][lnk-device-management].

Вы можете внедрить клиентские приложения на различных аппаратных платформах и в операционных системах с помощью пакетов SDK для устройств Azure IoT. Пакеты SDK для устройств включают библиотеки, упрощающие отправку данных телеметрии в Центр Интернета вещей и получение сообщений, отправляемых из облака на устройство. При использовании пакетов SDK для устройств вы можете выбрать любой из множества сетевых протоколов для взаимодействия с Центром Интернета вещей. Дополнительные сведения о пакетах SDK для устройств см. [здесь][lnk-device-sdks].

Начальное руководство по написанию кода и выполнению некоторых примеров см. в [этой статье][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Запись в блоге Клеменса Вастерса (Clemens Vasters) о взаимодействии с поддержкой службы"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md

