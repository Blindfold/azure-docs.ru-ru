---
title: "Руководство по интеграции Azure Active Directory с Zscaler One | Документация Майкрософт"
description: "Узнайте, как использовать Zscaler One вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f5640d84774898e1c51c5dcfa52aab781cddf044
ms.openlocfilehash: 22e6f8f13370ece01e8ef3b186f05033fb767f1b
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Руководство. Интеграция Azure Active Directory с Zscaler One
Цель данного учебника — показать интеграцию Azure и Zscaler One.  
 Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:  

* Действующая подписка на Azure
* Подписка с поддержкой единого входа ZScaler One  

По завершении работы с этим руководством пользователи Azure AD, назначенные в Zscaler One, смогут выполнять единый вход в приложение на веб-сайте Zscaler One компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).  

Сценарий, описанный в этом учебнике, состоит из следующих блоков:  

1. Включение интеграции приложений для Zscaler One 
2. Настройка единого входа
3. Настройка параметров прокси-сервера
4. Настройка подготовки учетных записей пользователей
5. Назначение пользователей  

![Сценарий](./media/active-directory-saas-zscaler-one-tutorial/IC800214.png "Сценарий")  

## <a name="enabling-the-application-integration-for-zscaler-one"></a>Включение интеграции приложений для Zscaler One 
В этом разделе показано, как включить интеграцию приложений для Zscaler One.  

### <a name="to-enable-the-application-integration-for-zscaler-one-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Zscaler One, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.  
   
   ![Active Directory](./media/active-directory-saas-zscaler-one-tutorial/IC700993.png "Active Directory")  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.  
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.  
   
   ![Приложения](./media/active-directory-saas-zscaler-one-tutorial/IC700994.png "Приложения")  
4. В нижней части страницы нажмите кнопку **Добавить** .  
   
   ![Добавление приложения](./media/active-directory-saas-zscaler-one-tutorial/IC749321.png "Добавление приложения")  
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.  
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-zscaler-one-tutorial/IC749322.png "Добавление приложения из коллекции")  
6. В **поле поиска** введите **Zscaler One**.  
   
   ![Коллекция приложений](./media/active-directory-saas-zscaler-one-tutorial/IC800215.png "Коллекция приложений")  
7. В области результатов выберите **Zscaler One** и нажмите кнопку **Завершить**, чтобы добавить приложение.  
   
   ![ZScaler One](./media/active-directory-saas-zscaler-one-tutorial/IC800216.png "ZScaler One")  

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Zscaler One со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат в кодировке Base-64 в клиент Zscaler One.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)  

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции **ZScaler One** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.  
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/IC800217.png "Настройка единого входа")  
2. На странице **Как пользователи должны входить в Zscaler One?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.  
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/IC800218.png "Настройка единого входа")  
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Zscaler One** введите URL-адрес, используемый пользователями для входа в приложение Zscaler One, и нажмите кнопку **Далее**.  
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-zscaler-one-tutorial/IC800219.png "Настройка URL-адреса приложения")  
   
   > [!NOTE]
   > Фактическое значение для вашей среды можно получить от службы поддержки Zscaler One.  
   > 
   > 
4. Чтобы скачать сертификат, на странице **Настройка единого входа в Zscaler One** щелкните **Скачать сертификат** и сохраните файл сертификата на компьютере.  
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/IC800220.png "Настройка единого входа")  
5. В другом окне браузера войдите на свой корпоративный сайт Zscaler One в качестве администратора.  
6. В верхнем меню щелкните **Администрирование**.  
   
   ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/IC800206.png "Администрирование")  
7. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Manage Users & Authentication** (Управление пользователями и проверкой подлинности).  
   
   ![Управление пользователями и проверкой подлинности](./media/active-directory-saas-zscaler-one-tutorial/IC800207.png "Управление пользователями и проверкой подлинности")  
8. В разделе **Выбор параметров проверки подлинности для организации** выполните следующие действия.  
   
   ![Аутентификация](./media/active-directory-saas-zscaler-one-tutorial/IC800208.png "Аутентификация")  
   
   1. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.  
   2. Щелкните **Настроить параметры единого входа SAML**.  
9. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните следующие действия и нажмите кнопку **Готово**.  
   
   ![Единый вход](./media/active-directory-saas-zscaler-one-tutorial/IC800209.png "Единый вход")  
   
   1. На странице диалогового окна **Настройка единого входа в Zscaler One** классического портала Azure скопируйте значение поля **URL-адрес запроса проверки подлинности** и вставьте его в текстовое поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для проверки подлинности).  
   2. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.  
   3. Чтобы передать скачанный сертификат, щелкните **Zscaler pem**.  
   4. Выберите параметр **Включить автоматическую подготовку SAML**.  
10. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.  
    
    ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/IC800210.png "Администрирование")  
    
    1. Щелкните **Сохранить**.  
    2. Щелкните **Активировать сейчас**.  
11. На классическом портале Azure на странице диалогового окна **Настройка единого входа в ZScaler One** подтвердите конфигурацию единого входа и нажмите кнопку **Завершить**.  
    
    ![Настройка единого входа](./media/active-directory-saas-zscaler-one-tutorial/IC800221.png "Настройка единого входа")  

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer
1. Запустите **Internet Explorer**.  
2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.  
   
   ![Свойства браузера](./media/active-directory-saas-zscaler-one-tutorial/IC769492.png "Свойства браузера")  
3. Щелкните вкладку **Подключения** .  
   
   ![Подключения](./media/active-directory-saas-zscaler-one-tutorial/IC769493.png "Подключения")  
4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.  
5. В разделе "Прокси-сервер" выполните следующие действия.  
   
   ![Прокси-сервер](./media/active-directory-saas-zscaler-one-tutorial/IC769494.png "Прокси-сервер")  
   
   1. Установите флажок "Использовать прокси-сервер для локальных подключений".  
   2. В текстовом поле "Адрес" введите **gateway.zscalerone.net**.  
   3. В текстовом поле "Порт" введите **80**.  
   4. Установите флаг **Не использовать прокси-сервер для локальных адресов**.  
   5. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.  
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.  

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли входить в ZScaler One, их необходимо подготовить для ZScaler One.  
 В случае с ZScaler One подготовка выполняется вручную.  

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите в клиент **Zscaler One** .  
2. Щелкните **Администрирование**.  
   
   ![Администрирование](./media/active-directory-saas-zscaler-one-tutorial/IC781035.png "Администрирование")  
3. Щелкните **Управление пользователями**.  
   
   ![Добавление](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Добавление")  
4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).  
   
   ![Добавление](./media/active-directory-saas-zscaler-one-tutorial/IC781037.png "Добавление")  
5. В разделе "Добавить пользователя" выполните следующие действия.  
   
   ![Добавление пользователя](./media/active-directory-saas-zscaler-one-tutorial/IC781038.png "Добавление пользователя")  
   
   1. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) действующей учетной записи AAD, которую необходимо подготовить.  
   2. Щелкните **Сохранить**.  

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя ZScaler One или API, предоставляемые ZScaler One для подготовки учетных записей пользователя AAD.  
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.  

### <a name="to-assign-users-to-zscaler-one-perform-the-following-steps"></a>Чтобы назначить пользователей ZScaler One, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.  
2. На странице интеграции с приложением **Zscaler One** нажмите кнопку **Назначить пользователей**.  
   
   ![Назначение пользователей](./media/active-directory-saas-zscaler-one-tutorial/IC800222.png "Назначение пользователей")  
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.  
   
   ![Да](./media/active-directory-saas-zscaler-one-tutorial/IC767830.png "Да")  

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).  


