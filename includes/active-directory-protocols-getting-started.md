---
title: "Обзор протокола .NET Azure AD | Документация Майкрософт"
description: "Использование HTTP-сообщений для авторизации доступа к веб-приложениям и веб-API в клиенте с использованием Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: f1e4b86a04a76513a2f0d9a9f89e49611c0447d5
ms.openlocfilehash: b31fa50a62d5b26a7346f212076ec3a2b0386f5e

---
## <a name="register-your-application-with-your-ad-tenant"></a>Регистрация приложения в клиенте AD
Для начала необходимо зарегистрировать приложение в клиенте Azure Active Directory (Azure AD). После этого вашему приложению будет присвоен идентификатор, и оно сможет получать маркеры.

* Войдите на [портал Azure](https://portal.azure.com).
* Выберите клиент Azure AD, щелкнув имя своей учетной записи в правом верхнем углу страницы.
* В области навигации слева щелкните **Azure Active Directory**.
* Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
* Следуйте инструкциям на экране, а затем создайте новое приложение. Для данного руководства не имеет значения, будет это веб-приложение или собственное приложение, но если вам нужны конкретные примеры веб-приложений или собственных приложений, ознакомьтесь с нашими [краткими руководствами](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Для веб-приложений укажите **URL-адрес входа**, который является базовым URL-адресом вашего приложения и через который пользователи могут входить в приложение, например `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Для собственных приложений укажите **URI перенаправления**который Azure AD будет использовать для возврата ответов маркеров. Укажите значение, специфичное для вашего приложения, например `http://MyFirstAADApp`
* После завершения регистрации система Azure AD присвоит приложению уникальный идентификатор клиента (код приложения). Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.

