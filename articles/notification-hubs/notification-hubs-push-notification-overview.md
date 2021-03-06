---
title: "Концентраторы уведомлений Azure"
description: "Узнайте, как добавить поддержку push-уведомлений для Центров уведомлений Azure."
author: ysxu
manager: erikre
editor: 
services: notification-hubs
documentationcenter: 
ms.assetid: fcfb0ce8-0e19-4fa8-b777-6b9f9cdda178
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 1/17/2017
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 58458cdc99cc8f78e63c9640a7c71a8ce5dc70f1
ms.openlocfilehash: a1be0b13cd1feb582a23965df142e44d90ac6851
ms.lasthandoff: 02/03/2017


---
# <a name="azure-notification-hubs"></a>Концентраторы уведомлений Azure
## <a name="overview"></a>Обзор
Центры уведомлений Azure предлагают удобный и масштабируемый механизм для push-уведомлений с поддержкой разных платформ. Один вызов API, единый для всех платформ, позволяет легко отправлять целевые персонализированные push-уведомления на любую мобильную платформу с любого облачного или локального сервера.

Центры уведомлений отлично походят как для отдельных потребителей, так и для крупных предприятий. Вот некоторые примеры задач, для которых наши клиенты применяют Центры уведомлений:

* отправка (с низкой задержкой) уведомлений об экстренных новостях миллионам пользователей;
* отправка купонов целевым сегментам пользователей с учетом их местоположения;
* отправка уведомлений о событиях пользователям или группам пользователей в информационных, спортивных, финансовых и игровых приложениях;
* передача в приложения рекламного содержимого, которое помогает привлекать клиентов и продавать товары;
* уведомление пользователей о корпоративных событиях, новостях и рабочих задачах;
* отправка кодов для многофакторной идентификации.

## <a name="what-are-push-notifications"></a>Что такое push-уведомления
Push-уведомления — это механизм взаимодействия приложения с пользователями, который позволяет передать пользователю мобильного приложения важную информацию, обычно во всплывающем или диалоговом окне. Как правило, пользователь имеет выбор — просмотреть сообщение или отказаться от просмотра. Если он выберет просмотр, откроется мобильное приложение, от которого поступило уведомление.

Push-уведомления жизненно важны для потребительских приложений, так как позволяют повысить качество и частоту взаимодействия с приложением. Для корпоративных приложений это важный метод передачи актуальной бизнес-информации. Это самый лучший способ взаимодействия с клиентами: он позволяет экономить заряд мобильного устройства, обеспечивает гибкость для отправителей сообщений и работает даже без запуска соответствующего приложения.

Дополнительные сведения о push-уведомлениях для нескольких популярных платформ:
* [iOS](https://developer.apple.com/notifications/)
* [Android](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)
* [Windows](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)

## <a name="how-push-notifications-work"></a>Как работают push-уведомления
Push-уведомления доставляются через инфраструктуры, предназначенные для конкретных платформ, которые известны как *системы отправки уведомлений платформы* (PNS). Они предоставляют базовые функциональные возможности для доставки push-уведомлений на конкретное устройство по предоставленному дескриптору, и не имеют единого интерфейса. Чтобы отправить уведомления всем клиентам, использующим разные версии приложения (для iOS, Android и Windows), разработчик должен выполнять пакетную отправку с помощью APNS (служба push-уведомлений Apple), FCM (Firebase Cloud Messaging) и WNS (служба уведомлений Windows).

В целом алгоритм отправки выглядит так:

1. Клиентское приложение решает, что нужно проверить наличие push-уведомлений. Для этого оно обращается к соответствующей службе PNS и получает временный уникальный дескриптор для push-уведомлений. Тип дескриптора зависит от системы (например, WNS использует URI, а APNS — маркеры).
2. Клиентское приложение сохраняет этот дескриптор в серверной части или у поставщика службы.
3. Для отправки push-уведомления серверная часть приложения обращается к PNS, используя дескриптор для обращения к приложению конкретного клиента.
4. PNS перенаправляет уведомление на устройство, указанное дескриптором.

![][0]

## <a name="the-challenges-of-push-notifications"></a>Трудности в работе с push-уведомлениями
Хотя системы PNS являются очень мощным средством, разработчикам приложений требуется прилагать много усилий для реализации даже распространенных сценариев использования push-уведомлений, например для отправки push-уведомлений всем пользователям или определенным сегментам пользователей.

Это одна из самых востребованных функций мобильных облачных служб, так как для ее работы нужна сложная инфраструктура, не связанная с основной бизнес-логикой приложения. Вот лишь некоторые из трудностей, которые возникают на уровне инфраструктуры.

* **Зависимость от платформы.** 

  * Серверная служба вынуждена использовать сложную и неудобную в обслуживании логику работы с разными платформами, чтобы отправлять уведомления на все возможные устройства, так как системы PNS не стандартизированы.
* **Масштабируемость.**

  * По правилам PNS маркеры устройств необходимо обновлять при каждом запуске приложения. Это означает, что на серверную часть направляется большой объем трафика и нужно много обращений к базе данных только для того, чтобы поддерживать актуальность маркеров. Если количество устройств достигает сотен миллионов или даже миллиардов, требуются огромные затраты на создание и поддержание такой инфраструктуры.
  * Большинство систем PNS не поддерживают рассылку на несколько устройств. Чтобы разослать самое простое сообщение на миллион устройств, потребуется миллион обращений к разным PNS. Передача таких объемов трафика с минимальными задержками — непростая задача.
* **Маршрутизация.**
  
  * Системы PNS предоставляют средства для отправки сообщений на устройства, но для большинства приложений нужна еще и возможность сегментирования по пользователям или тематическим группам. Это означает, что серверная часть должна поддерживать реестр устройств с привязкой к тематическим группам, пользователям, свойствам и т. д. Эти сложности увеличивают время выхода приложения на рынок и повышают затраты на его обслуживание.

## <a name="why-use-notification-hubs"></a>Зачем использовать центры уведомлений
Центры уведомлений устраняют все сложности, которые возникают при самостоятельной работе с push-уведомлениями. Многоплатформенная масштабируемая инфраструктура для отправки push-уведомлений позволяет сократить объем кода, нацеленный на использование push-уведомлений, и существенно упростить серверную часть приложения. Если вы используете Центры уведомлений, вам остается лишь зарегистрировать с устройства дескрипторы PNS, а затем отправлять сообщения от серверной части нужным пользователям или группам пользователей. Эта схема работы представлена на следующем рисунке.

![][1]

Центры уведомлений — это готовые к использованию механизмы отправки push-уведомлений, которые обеспечивают следующие преимущества.

* **Кроссплатформенная поддержка**

  * Поддерживаются все основные платформы, использующие push-уведомления, включая iOS, Android, Windows, Kindle и Baidu.
  * Единый интерфейс для отправки уведомлений на все платформы. Вы можете использовать отдельные форматы для разных платформ или единый формат, и при этом не требуются дополнительные действия с учетом особенностей платформ.
  * Управление маркерами устройств выполняется централизованно.
* **Поддержка различных серверных систем**
  
  * Облачные или локальные системы.
  * .NET, Node.js, Java и т. д.
* **Широкий набор схем доставки**:

  * *Рассылка на одну или несколько платформ*. Вы можете одним вызовом API мгновенно отправить сообщение на несколько миллионов устройств, в том числе на разных платформах.
  * *Push-уведомление на устройство*. Вы можете направлять уведомления на конкретные устройства.
  * *Push-уведомление для пользователя*. Поддержка тегов и шаблонов позволяет передать сообщение конкретному пользователю, использующему несколько устройств на разных платформах.
  * *Push-уведомления для сегмента пользователей с использованием динамических тегов*. Поддержка тегов позволяет разделять устройства на сегменты и отправлять уведомления для любого из этих сегментов или любой комбинации сегментов, используя логические выражения (например, для всех пользователей, которые активны И живут в Сиэтле И НЕ являются новыми). Вы больше не ограничены шаблоном "издатель — подписчик" и можете обновлять теги для устройств когда угодно и где угодно.
  * *Локализованные push-уведомления*. Поддержка шаблонов позволяет локализовать систему, не внося изменения в код серверной части.
  * *Автоматические push-уведомления*. Вы можете использовать механизм push-to-pull, рассылая на устройства автоматические уведомления, которые инициируют обмен информацией или выполнение действий на этих устройствах.
  * *Запланированные push-уведомления*. Вы можете назначить отправку уведомлений на любое время.
  * *Технология Direct Push*. При использовании нашей службы вы можете даже не регистрировать устройства, а просто отправлять уведомления по списку дескрипторов устройств.
  * *Персонализированные push-уведомления*. Переменные в push-уведомлениях с информацией об устройстве позволяют отправлять персонализированные push-уведомления, настроив пары "ключ — значение".
* **Богатые возможности телеметрии**
  
  * Общие данные телеметрии по уведомлениям, устройствам, ошибкам и операциям можно получать через портал Azure и с помощью программных средств.
  * Телеметрия для каждого сообщения позволяет отследить каждое push-уведомление, отправляемое в соответствии с пакетным заданием, переданным нам в исходном запросе.
  * Обратная связь от системы отправки уведомлений платформы позволяет получить всю информацию, необходимую для отладки.
* **Масштабируемость** 
  
  * Быстрая отправка сообщений на миллионы устройств, для которой не потребуется изменять архитектуру системы или сегментировать устройства.
* **Безопасность**

  * Поддерживаются Shared Access Secret (SAS) и федеративная проверка подлинности.

## <a name="integration-with-app-service-mobile-apps"></a>Интеграция с мобильными приложениями службы приложений
Для упрощения и унификации взаимодействия между службами Azure [мобильные приложения службы приложений] располагают встроенной поддержкой push-уведомлений с помощью центров уведомлений. [мобильные приложения службы приложений] — это доступная по всему миру высокомасштабируемая платформа разработки мобильных приложений, предназначенная для корпоративных разработчиков и системных интеграторов и расширяющая возможности разработки мобильных приложений.

Разработчики мобильных приложений могут использовать концентраторы уведомлений в следующем рабочем процессе.

1. Получение дескриптора PNS устройства.
2. Регистрируйте устройства в Центрах уведомлений с помощью удобного API регистрации, входящего в клиентский пакет SDK для мобильных приложений.
   * Обратите внимание, что в целях безопасности мобильные приложения удаляют в регистрациях все теги. Работа с концентраторами уведомлений непосредственно на стороне сервера для связывания тегов с устройствами.
3. Отправка уведомлений из серверной части приложения с помощью концентраторов уведомлений.

Далее перечислены некоторые преимущества, получаемые разработчиками в рамках интеграции:

* **Клиентские SDK для мобильных приложений**. Эти многоплатформенные пакеты SDK предоставляют простые API-интерфейсы, которые позволяют автоматически обращаться к нужному центру уведомлений для регистрации устройств и передачи информации. Разработчикам не нужно хранить учетные данные для разных концентраторов уведомлений и прибегать к дополнительной службе.

  * *Push-уведомления для пользователя*. Пакеты SDK автоматически поддерживают для каждого устройства теги с идентификаторами проверенных пользователей мобильных приложений. Это позволяет отправлять push-сообщения конкретным пользователям.
  * *Push-уведомление на устройство*. Пакеты SDK автоматически отслеживают идентификатор установки мобильных приложений при регистрации в Центрах уведомлений. Это упрощает работу разработчикам, избавляя их от необходимости поддерживать несколько GUID для разных служб.
* **Модель установки**. Мобильные приложения работают с новейшей моделью Центров уведомлений. В ней в формате JSON хранятся все свойства уведомлений, связанные с устройством. Такая схема удобна в использовании и совместима со службами push-уведомлений.
* **Гибкость**. Даже настроив интеграцию, разработчик всегда может напрямую обратиться к Центрам уведомлений.
* **Интегрированный интерфейс на [портале Azure]**. Отправка push-уведомлений как функция визуально представлена в мобильных приложениях. Разработчики могут легко взаимодействовать через мобильные приложения с Центром уведомлений.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о центрах уведомлений можно найти в следующих статьях:

* **[Центры уведомлений]**
* **[Документация центров уведомлений]**
* **Руководства по началу работы с Центрами уведомлений**: [iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android].

[0]: ./media/notification-hubs-overview/registration-diagram.png
[1]: ./media/notification-hubs-overview/notification-hub-diagram.png
[Центры уведомлений]: http://azure.microsoft.com/services/notification-hubs
[Документация центров уведомлений]: http://azure.microsoft.com/documentation/services/notification-hubs
[iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
[Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
[Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
[Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
[Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
[Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
[Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
[Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
[Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
[мобильные приложения службы приложений]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
[templates]: notification-hubs-templates-cross-platform-push-messages.md
[портале Azure]: https://portal.azure.com
[tags]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

