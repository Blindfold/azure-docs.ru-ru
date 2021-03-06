---
title: "Что такое Microsoft Azure Active Directory"
description: "Используйте Azure Active Directory, чтобы расширить возможности существующих локальных удостоверений в облаке или разрабатывать приложения, интегрируемые с Azure AD."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: b40ae90ea313638cbd0b60792dc4803d3d08aa0a
ms.openlocfilehash: 03c1442daf07f57476af64491229f1f38f6ffeff
ms.contentlocale: ru-ru
ms.lasthandoff: 02/24/2017


---
# <a name="what-is-azure-active-directory"></a>Что такое Microsoft Azure Active Directory
Azure Active Directory (Azure AD) — многопользовательский облачный каталог и служба управления удостоверениями корпорации Майкрософт.

Azure AD предоставляет ИТ-администраторам доступное и удобное в использовании решение, с помощью которого они могут давать сотрудникам и деловым партнерам компании доступ на основе единого входа к [тысячами облачных приложений SaaS](active-directory-saas-tutorial-list.md) , таким как Office 365, Salesforce.com, DropBox и Concur.

Разработчикам приложений Azure AD позволяет сосредоточиться на создании приложений и предоставляет возможность быстро и легко интегрировать приложения с решением мирового класса для управления удостоверениями, которое используют миллионы организаций по всему миру.

Azure AD также включает полный набор возможностей управления удостоверениями, в том числе многофакторную проверку подлинности, регистрацию устройств, самостоятельное управление паролями, самостоятельное управление группами, управление привилегированной учетной записью, управление доступом на основе ролей, отслеживание использования приложений, расширенный аудит, мониторинг безопасности и предупреждения. Эти возможности помогают защитить облачные приложения, упростить ИТ-процессы, сократить расходы и обеспечить соблюдение корпоративных требований.

Кроме того, Azure AD можно интегрировать с существующей службой Windows Server Active Directory всего [четырьмя щелчками](./connect/active-directory-aadconnect-get-started-express.md). Благодаря этому организации смогут извлекать выгоду из своих инвестиций в существующие локальные удостоверения для управления доступом к облачным приложениям SaaS.

Если вы используете Office 365, Azure или Dynamics CRM Online, вы можете не знать, что вы уже используете Azure AD. Каждый пользователь Office 365, Azure и Dynamics CRM фактически является пользователем Azure AD. Вы можете когда угодно начать использовать этот клиент для управления доступом к тысячам других облачных приложений, с которыми интегрируется Azure AD.

![Стек Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Насколько надежна служба Azure AD?
Многопользовательскую, географически распределенную и высокодоступную структуру Azure AD можно использовать для наиболее важных бизнес-задач. Инфраструктура Azure AD размещена по всему миру в 28 центрах обработки данных с автоматической обработкой отказа, поэтому вы можете быть уверены в надежности Azure AD. Даже если центр обработки данных выйдет из строя, копии данных каталога хранятся по крайней мере в двух дополнительных географически разделенных центрах обработки данных и доступны для мгновенного доступа.

Дополнительные сведения можно найти в разделе [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/).

## <a name="what-are-the-benefits-of-azure-ad"></a>Каковы преимущества Azure AD?
Организация может по-разному использовать Azure AD для повышения производительности сотрудников, упрощения ИТ-процессов, повышения уровня безопасности и снижения расходов.

* Быстрое внедрение облачных служб, которые предоставляют сотрудникам и партнерам простой интерфейс с единым входом на основе полностью автоматизированного управления доступом Azure AD к приложениям SaaS и с возможностями подготовки служб.
* Предоставление сотрудникам доступа к облачным приложениям мирового класса и возможностям самообслуживания в любом месте и на любых устройствах.
* Простое и безопасное управление доступом сотрудников и поставщиков к учетным записям корпоративных социальных сетей.
* Повышение безопасности приложений с помощью многофакторной проверки подлинности и условного доступа Azure AD.
* Внедрение согласованного и самостоятельного управления доступом к приложениям, которое позволяет владельцам компаний существенно сократить затраты на ИТ и непроизводственные расходы.
* Мониторинг использования приложений и защита от дополнительных угроз с отчетами и мониторингом безопасности.
* Защита мобильного (удаленного) доступа к локальным приложениям.

## <a name="how-does-azure-ad-compare-to-on-premises-active-directory-domain-services-ad-ds"></a>В чем заключаются сходство и отличие Azure AD и локальных доменных служб Active Directory (AD DS)?

Azure Active Directory (Azure AD) и локальный каталог Active Directory (доменные службы Active Directory, или AD DS) — это системы, которые хранят данные каталога и управляют взаимодействием между пользователями и ресурсами, включая процессы входа пользователей, аутентификацию и поиск в каталоге.

Службы AD DS выполняют роль сервера в Windows Server. Это означает, что их можно развернуть на физических компьютерах или виртуальных машинах. Они имеют иерархическую структуру, основанную на стандарте X.500. В службах AD DS используется DNS для поиска объектов, с ними может взаимодействовать с помощью LDAP, а для аутентификации, как правило, используется протокол Kerberos. Active Directory позволяет использовать подразделения и объекты групповой политики (GPO) наряду с присоединением компьютеров и виртуальных машин к домену, а также устанавливать отношения доверия между доменами.

Azure AD — это общедоступная служба каталога для нескольких клиентов. Это означает, что в Azure AD можно создать клиент для облачных серверов и приложений, таких как Office 365. Пользователи и группы создаются в виде плоской структуры без подразделений и объектов групповой политики. Аутентификация выполняется с помощью таких протоколов, как SAML, WS-Federation и OAuth. Существует возможность выполнить запрос к Azure AD, но вместо LDAP необходимо использовать интерфейс REST API под названием API AD Graph. Все эти операции выполняются посредством протоколов HTTP и HTTPS.




## <a name="how-can-i-get-started"></a>Как начать работу?

**Если вы ИТ-администратор:**

* [Просто попробуйте!](https://azure.microsoft.com/trial/get-started-active-directory/) Вы можете зарегистрироваться сегодня, чтобы использовать бесплатную пробную версию в течение 30 дней и развернуть первое облачное решение в течение 5 минут, применив эту ссылку.

* Прочтите статью "Начало работы с Azure AD", чтобы ознакомиться с советами и рекомендациями по началу работы и быстрому запуску клиента Azure AD.

**Если вы разработчик:**
 
* Ознакомьтесь с нашим [руководством разработчика](active-directory-developers-guide.md) по Azure Active Directory.

* [Начните использовать пробную версию](https://azure.microsoft.com/trial/get-started-active-directory/) — зарегистрируйтесь сегодня, чтобы использовать бесплатную пробную версию в течение 30 дней и приступить к интеграции приложения с Azure AD.

## <a name="where-can-i-learn-more"></a>Где можно получить дополнительную информацию?
В сети есть множество полезных ресурсов, которые помогут вам больше узнать об Azure AD. Ниже приведен список отличных статей для начала работы.

* [Обеспечение гибридного управления для каталога с помощью Azure AD Connect](active-directory-aadconnect.md)
* [Повышенная безопасность для постоянного подключения к Интернету](../multi-factor-authentication/multi-factor-authentication.md)
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Приступая к работе с помощью Azure AD Reporting](active-directory-reporting-getting-started.md)
* [Управление паролями из любой точки мира](active-directory-passwords.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Как обеспечить безопасный удаленный доступ к локальным приложениям](active-directory-application-proxy-get-started.md)
* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Что такое лицензирование Microsoft Azure Active?](active-directory-licensing-what-is.md)
* [Как обнаруживать несанкционированные облачные приложения, которые используются в моей организации](active-directory-cloudappdiscovery-whatis.md)

