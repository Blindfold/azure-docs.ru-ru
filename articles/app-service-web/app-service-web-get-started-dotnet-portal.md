---
title: "Развертывание веб-приложения Umbraco на портале Azure за пять минут | Документация Майкрософт"
description: "Убедитесь, как просто запускать веб-приложения в службе приложений, развернув пример приложения ASP.NET. Кроме того, вы сможете быстро просмотреть результаты."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.lasthandoff: 04/06/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Развертывание веб-приложения Umbraco на портале Azure за пять минут

Это руководство поможет вам за считанные минуты развернуть простое веб-приложение [Umbraco](https://our.umbraco.org/) в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md).

![Приложение Umbraco](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>Предварительные требования
Вам потребуется учетная запись Microsoft Azure. Если у вас нет учетной записи, [подпишитесь на бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) или [активируйте преимущества для подписчиков Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

> [!NOTE]
> [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/) возможно даже без учетной записи Azure. Вы можете создать приложение начального уровня и экспериментировать с ним в течение часа. Для этого вам не нужно указывать данные кредитной карты или брать на себя какие-либо обязательства.
> 
> 

## <a name="deploy-the-aspnet-app"></a>Развертывание приложения ASP.NET
1. Войдите на [портал Azure](https://portal.azure.com).

2. Откройте страницу [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS).

    Эта ссылка позволит сразу перейти на страницу настройки нового приложения Umbraco на портале Azure.

3. В поле **Имя приложения** введите имя веб-приложения. Если имя является уникальным в домене `azurewebsites.net`, то в поле появится зеленая галочка.
   
5. В разделе **Группа ресурсов** щелкните **Создать**, чтобы создать [группу ресурсов](../azure-resource-manager/resource-group-overview.md), и введите ее имя.

7. Щелкните **Расположение или план службы приложений** > **Создать**. Настройте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), как показано ниже.

    - В поле **План службы приложений** введите требуемое имя.
    - Из списка **Расположение** выберите расположение для размещения плана.
    - Щелкните **Ценовая категория**, а затем выберите **F1 Free** или другую категорию, которая вам подходит. Щелкните **Выбрать**.
    - Нажмите кнопку **ОК**.

    Конфигурация Umbraco CMS должна выглядеть как на следующем снимке экрана.

    ![Настройка конфигурации: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. Щелкните **База данных SQL** > **Создать новую базу данных**. Настройте базу данных SQL, как показано ниже.

    - В поле **Имя** введите имя, например **myDB**.
    - Щелкните **Ценовая категория**, а затем выберите **F1 Free** или другую категорию, которая вам подходит. Щелкните **Выбрать**.
    - Щелкните **Целевой сервер** > **Создать сервер**. Настройте сервер базы данных, как показано, ниже.

        - В поле **Имя сервера** введите имя сервера. Если имя является уникальным в домене `.database.windows.net`, то в поле появится зеленая галочка.
        - В поле **Имя для входа администратора сервера** введите требуемое имя пользователя для администратора.
        - В полях **Пароль** и **Подтверждение пароля** введите требуемый пароль.
        - Из списка "Расположение" выберите расположение, указанное для веб-приложения.
        - Убедитесь, что установлен флажок **Разрешить службам Azure доступ к серверу**.
        - Нажмите кнопку **Выбрать**.
    
    - Нажмите кнопку **Выбрать**.

13. Щелкните **Параметры веб-приложения**, укажите имя пользователя и пароль для базы данных, затем нажмите кнопку **ОК**.

    Конфигурация Umbraco CMS должна выглядеть как на следующем снимке экрана.

    ![Настройка конфигурации завершена: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. Щелкните **Создать**.
    
    Теперь Azure создаст приложение Umbraco на основе выбранной конфигурации. Должно появиться уведомление с текстом **Развертывание начато**.

    ![Успешное развертывание: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Запуск веб-приложения Umrbaco и управление им

Когда Azure завершит развертывание приложения, появится еще одно уведомление.

![Успешное развертывание: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. Щелкните это уведомление. Если вы пропустили его, то всегда сможете открыть это уведомление, щелкнув значок уведомления (звонок). (![Уведомление внизу: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/notification.png).)

    Теперь должна отображаться [колонка](../azure-resource-manager/resource-group-portal.md#manage-resources) управления веб-приложением. (*Колонка*: горизонтальная страница портала.)

3. В верхней части страницы обзора щелкните **Обзор**.
   
    ![Обзор: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/browse.png)

    Теперь вы видите страницу **приветствия** Umbraco. Настройте установленное приложение Umbraco и поэкспериментируйте с ним.

    ![Конфигурация Umbraco: первое приложение Umbraco в службе приложений Azure](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание веб-приложения ASP.NET в службе приложений Azure с помощью Visual Studio](app-service-web-get-started-dotnet.md). Узнайте, как создать веб-приложение в Visual Studio с помощью любого из предоставляемых шаблонов приложения.
* [Развертывание приложения в службе приложений Azure](web-sites-deploy.md). Узнайте, как развернуть свой код из FTP или репозиториев системы управления версиями.
* [Добавление функциональных возможностей в первое веб-приложение](app-service-web-get-started-2.md). Выведите приложение Azure на следующий уровень. Проверяйте подлинность пользователей. Масштабируйте приложение в зависимости от потребностей. Настраивайте оповещения производительности. И все это — с помощью нескольких действий.

