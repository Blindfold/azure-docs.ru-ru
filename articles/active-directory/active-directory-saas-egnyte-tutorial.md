---
title: "Учебник. Интеграция Azure Active Directory с Egnyte | Документация Майкрософт"
description: "Узнайте, как использовать Egnyte вместе с Azure Active Directory для реализации единого входа, автоматической подготовки и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 4e8857244038435430f3cabdf5f3eccad8a922bf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Руководство. Интеграция Azure Active Directory с Egnyte
Цель данного руководства — показать интеграцию Azure и Egnyte.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* Подписка Egnyte с поддержкой единого входа

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к Egnyte, смогут использовать единый вход в приложение на веб-сайте Egnyte вашей организации (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md)

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

* Включение интеграции приложений для Egnyte
* Настройка единого входа
* Настройка подготовки учетных записей пользователей
* Назначение пользователей

![Сценарий](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Сценарий")

## <a name="enable-the-application-integration-for-egnyte"></a>Включение интеграции приложений для Egnyte
В этом разделе показано, как включить интеграцию приложений для Egnyte.

**Чтобы включить интеграцию приложений для Egnyte, сделайте следующее:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **egnyte**.
   
   ![Коллекция приложений](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Коллекция приложений")
7. В области результатов выберите **Egnyte** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
   
## <a name="configure-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Egnyte со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Egnyte** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Egnyte?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Egnyte** введите свой URL-адрес в формате *https://компания.egnyte.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Egnyte** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Настройка единого входа")
5. В другом окне браузера войдите на свой корпоративный веб-сайт Egnyte в качестве администратора.
6. Щелкните **Параметры**.
   
   ![Параметры](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Параметры")
7. В меню выберите пункт **Параметры**.
   
   ![Параметры](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Параметры")
8. Откройте вкладку **Configuration** (Конфигурация) и нажмите элемент **Security** (Безопасность).
   
   ![Безопасность](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Безопасность")
9. В разделе **Проверка подлинности единого входа** выполните следующие действия.
   
   ![Аутентификация единого входа](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Аутентификация единого входа")   
   1. Выберите для параметра **Single sign-on authentication** (Аутентификация единого входа) значение **SAML 2.0**.
   2. Выберите для параметра **Identity provider** (Поставщик удостоверений) значение **AzureAD**.
   3. На странице диалогового окна **Настройка единого входа в Egnyte** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Identity provider login URL ** (URL-адрес входа поставщика удостоверений).
   4. На странице диалогового окна **Настройка единого входа в Egnyte** классического портала Azure скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **Identity provider entity ID** (Идентификатор сущности поставщика удостоверений).
   5. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
     >[!TIP]
     >Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
   6. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений** .
   7. Выберите для параметра **Default user mapping** (Сопоставление пользователя по умолчанию) значение **Email address** (Адрес электронной почты).
   8. Выберите для параметра **Use domain-specific issuer value** (Использовать значение издателя домена) значение **disabled** (отключено).
   9. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
   ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Настройка единого входа")
    
## <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Egnyte, они должны быть подготовлены для Egnyte. В случае с Egnyte подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Выполните вход на веб-сайт компании **Egnyte** в качестве администратора.
2. Последовательно выберите пункты **Settings \> Users & Groups** (Параметры > Пользователи и группы).
3. Нажмите **Добавить пользователя**, а затем выберите тип для пользователя, которого желаете добавить.
   
   ![Пользователи](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Пользователи")
4. В разделе **Новый обычный пользователь** выполните следующие действия.
   
   ![Новый обычный пользователь](./media/active-directory-saas-egnyte-tutorial/IC787825.png "Новый обычный пользователь")   
   1. Введите значения **Email** (Адрес электронной почты), **Username** (Имя пользователя) и другие данные действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.
    >[!NOTE]
    >Владелец учетной записи Azure Active Directory получит уведомление по электронной почте.
    >

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Egnyte или API, предоставляемые Egnyte для подготовки учетных записей пользователя AAD.
> 

## <a name="assign-users"></a>Назначить пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей Egnyte, сделайте следующее:**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Egnyte** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


