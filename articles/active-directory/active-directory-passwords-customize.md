---
title: "Настройка Azure AD SSPR | Документация Майкрософт"
description: 
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 41da786ed13308a1fc030e6b02fac3d8fbca9e61
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Настройка функции самостоятельного сброса паролей в Azure AD

В этом руководстве описаны параметры, связанные с самостоятельным сбросом пароля (SSPR), которые можно изменить в Azure AD.

## <a name="customize-the-contact-your-administrator-link"></a>Настройка ссылки "Обратитесь к администратору"

Даже если SSPR не включен, можно воспользоваться ссылкой "Обратитесь к администратору" на портале сброса паролей.  После выбора этой ссылки администратору будет отправлено сообщение электронной почты с запросом на помощь в изменении пароля пользователя. Это сообщение электронной почты отправляется следующим получателям в следующем порядке:

1. Уведомление получат пользователи с ролью **Администратор паролей**.
2. Если администраторы паролей отсутствуют, уведомление получат пользователи с ролью **Администратор пользователей**.
3. Если ни одна из вышеупомянутых ролей не назначена, уведомление получат **глобальные администраторы**.

Во всех случаях уведомление будет передано не более чем 100 получателям.

### <a name="disable-contact-your-administrator-emails"></a>Отключение отправки электронных сообщений при выборе ссылки "Обратитесь к администратору"

Если организации не требуется уведомлять администраторов о запросах на сброс паролей, можно включить следующую конфигурацию.

* Включение самостоятельного сброса пароля для всех пользователей Этот параметр находится в разделе **Сброс пароля**, **Свойства**
    * Если вы не хотите, чтобы пользователи могли сбрасывать пароли, можно ограничить доступ любой пустой группой (не рекомендуемый параметр**).
* Настройте ссылку на службу технической поддержки для предоставления URL-адреса или адреса mailto, который пользователи могут использовать для получения помощи. Последовательно выберите **Сброс пароля**, **Настройка**, а затем **Адрес электронной почты или URL-адрес нестандартной службы технической поддержки**

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Настройка выполнения входа и оформления панели доступа

Вы можете настроить логотип, который отображается вместе с изображением страницы входа, в соответствии с фирменной символикой организации.

Эти изображения отображаются в следующих случаях:

* После того, как пользователь вводит свое имя пользователя.
* Когда пользователь обращается к настраиваемому URL-адресу.
    * При добавлении параметра whr к адресу страницы сброса паролей, например https://login.microsoftonline.com/?whr=contoso.com.
    * При добавлении параметра username к адресу страницы сброса паролей, например https://login.microsoftonline.com/?username=admin@contoso.com.

### <a name="graphics-details"></a>Сведения о графических изображениях

Следующие параметры позволяют изменять визуальные характеристики страницы входа. Их можно найти в разделе **Azure Active Directory**, **Корпоративная фирменная символика**, **Изменение корпоративной фирменной символики**.

* Изображение страницы входа должно быть PNG или JPG-файлом размера 1420 x 1200 пикселей не более 500 КБ. Для достижения наилучших результатов мы рекомендуем использовать изображение около 200 КБ.
* Цвет фона страницы входа используется при подключениях с высокой задержкой. Он должен быть в шестнадцатеричном формате RGB.
* Изображение баннера должно быть PNG- или JPG-файлом размера 60 x 280 пикселей не более 10 КБ.
* Квадратный логотип (обычная и темная темы), PNG- или JPG-файл, 240 x 240 пикселей (изменяемое значение), не более 10 КБ.

### <a name="sign-in-text-options"></a>Параметры текста входа

Следующие параметры позволяют добавить на страницу входа текст, относящийся к вашей организации. Эти параметры можно найти в разделе **Azure Active Directory**, **Корпоративная фирменная символика**, **Изменение корпоративной фирменной символики**.

* **Подсказка по имени пользователя** заменяет текст примера someone@example.com чем-то более подходящим для пользователей. Мы рекомендуем оставить параметр по умолчанию при поддержке внутренних и внешних пользователей.
* **Текст страницы входа** должен быть не более 256 символов в длину. Он будет отображаться, когда пользователь будет выполнять вход в сеть и подключаться к Azure AD в Windows 10. Используйте этот текст для условий использования, инструкций и советов для пользователей. **Любой пользователь может видеть страницу входа в систему. Поэтому не размещайте на ней никаких конфиденциальных сведений.**

### <a name="keep-me-signed-in-disabled"></a>Возможность оставаться в системе отключена

Параметр "Возможность оставаться в системе отключена" позволяет пользователю продолжать работу после того, как он закрыл и снова открыл окно браузера. Не влияет на время действия сеанса. Этот параметр можно найти в разделе **Azure Active Directory**, **Корпоративная фирменная символика**, **Изменение корпоративной фирменной символики**.

Некоторые функции SharePoint Online и Office 2010 зависят от возможности пользователей установить этот флажок. Если скрыть этот параметр, пользователи могут получать непредвиденные дополнительные запросы на выполнение входа.

### <a name="directory-name"></a>Имя каталога

Имя атрибута можно изменить в разделе **Azure Active Directory**, **Свойства** для отображения понятного названия организации на портале и при автоматическом взаимодействии. Этот параметр лучше заметен в виде автоматических сообщений электронной почты в следующих формах:

* Понятное имя в сообщении электронной почты "Майкрософт от демонстрационной учетной записи CONTOSO".
* Тема в сообщении электронной почты "Электронное письмо с кодом проверки демонстрационной учетной записи CONTOSO".

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сбросе пароля с помощью Azure AD см. в следующих источниках:

* [**Приступая к работе с компонентами управления паролями**](active-directory-passwords-getting-started.md). Запуск и выполнение службы самостоятельного управления паролями Azure AD. 
* [**Licensing requirements for Azure AD self-service password reset**](active-directory-passwords-licensing.md) (Требования к лицензированию самостоятельного сброса пароля в Azure AD). Сведения о настройке лицензирования Azure AD.
* [**Deploy password reset without requiring end-user registration**](active-directory-passwords-data.md) (Развертывание сброса пароля без регистрации пользователя). Сведения о необходимых данных и их использовании для управления паролями.
* [**Развертывание функции сброса паролей для пользователей**](active-directory-passwords-best-practices.md). Рекомендации по планированию и развертыванию SSPR для пользователей.
* [**Политики и ограничения для паролей в Azure Active Directory**](active-directory-passwords-policy.md). Общие сведения и информация об установке политик паролей Azure AD.
* [**Обзор компонента обратной записи паролей**](active-directory-passwords-writeback.md). Как работает функция обратной записи паролей с вашим локальным каталогом.
* [**Параметры отчетов для управления паролями Azure AD**](active-directory-passwords-reporting.md). Определяйте, кто и когда использовал функцию SSPR.
* [**Как работает управление паролями в Azure Active Directory**](active-directory-passwords-how-it-works.md). Сведения о принципе работы управления паролями.
* [**Вопросы и ответы об управлении паролями**](active-directory-passwords-faq.md). Как? Почему? Что? Где? Кто? Когда? Здесь приведены ответы на интересующие вас вопросы.
* [**Устранение неполадок, связанных с управлением паролями**](active-directory-passwords-troubleshoot.md). Сведения об устранении распространенных проблем с SSPR.


