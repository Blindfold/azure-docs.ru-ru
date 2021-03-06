---
title: "Техническая информация об условном доступе в Azure Active Directory | Документация Майкрософт"
description: "С условным контролем доступа при проверке подлинности пользователя и перед предоставлением ему доступа к приложению Azure Active Directory проверяет определенные условия, которые вы можете выбрать. Если эти условия выполняются, пользователь проходит проверку подлинности, и ему дается доступ к приложению."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4296bbc7123f6571ad564351612864a6315d8abf
ms.openlocfilehash: 450f3e001a0bc4a45fea4c4f0a81e676e9a80cc4
ms.lasthandoff: 03/02/2017


---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Техническая информация об условном доступе в Azure Active Directory.

## <a name="services-enabled-with-conditional-access"></a>Службы, включаемые с условным доступом

Правила условного доступа поддерживаются в самых разных типах приложений Azure AD. В этот список входят:


* Приложения, зарегистрированные с прокси приложений Azure
* Удаленное приложение Azure
* Линейка многопользовательских и бизнес-приложений, зарегистрированных в Azure AD
* Dynamics CRM
* Федеративные приложения из коллекции приложений Azure AD
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (включая OneDrive для бизнеса)
* Microsoft Power BI 
* Приложения с единым входом и паролем из коллекции приложений Azure AD
* Visual Studio Online









## <a name="enable-access-rules"></a>Включить правила доступа
Каждое правило можно включать или отключать для каждого приложения отдельно. Если правила **включены** , они будут действовать для всех пользователей, которые обращаются к приложению. Если правила **отключены** , они не действуют и не влияют на то, как пользователи входят в систему.

## <a name="applying-rules-to-specific-users"></a>Применение правил к определенным пользователям
Правила могут применяться для ряда пользователей, входящих в определенную группу безопасности. Для этого используется параметр **Применить к**. Для параметра **Применить к** может быть задано значение **Все пользователи** или **Группы**. Если задано значение **Все пользователи**, правила будут применяться к любому пользователю, имеющему доступ к приложению. Параметр **Группы** позволяет выбрать определенные группы и группы рассылки — правила будут применяться только к выбранным группам.

Обычно при развертывании правило сначала применяется к ограниченному кругу пользователей, входящих в пилотные группы. После завершения правила могут применяться к группе **Все пользователи**. В результате правило будет действовать для всех пользователей в организации.

Некоторые группы можно освободить от подчинения правилу с помощью параметра **Исключение** . Все члены этих групп будет исключены, даже если они входят в какую-либо включенную группу.

## <a name="at-work-networks"></a>Сети "на работе"
Правила условного доступа с использованием сети "на работе" действуют на основе диапазонов доверенных IP-адресов, которые настроены в Azure AD, или используют утверждение "в пределах корпоративной сети" из AD FS. В эти правила входит следующее:

* Требовать многофакторную проверку подлинности, если пользователь находится не на работе.
* Блокировать доступ, если пользователь находится не на работе.

Как указать сети "на работе"

1. Настройте диапазоны доверенных IP-адресов на [странице настройки многофакторной проверки подлинности](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Политика условного доступа использует настроенные диапазоны IP-адресов при каждом запросе проверки подлинности и выдаче токенов для оценки правил. 
2. Настройте использование утверждения "в пределах корпоративной сети". Этот параметр можно использовать с федеративными каталогами с применением AD FS. Дополнительные сведения об утверждениях "в пределах корпоративной сети" см. в разделе [Надежные IP-адреса](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="rules-based-on-application-sensitivity"></a>Правила, учитывающие уровень конфиденциальности приложений
Правила настраиваются для каждого приложения отдельно, что позволяет защищать особо значимые службы, не препятствуя доступу к другим службам. Правила условного доступа можно настроить на вкладке **Настройка** приложения. 

Далее указаны правила, предлагаемые в настоящее время:

* **Требовать многофакторную проверку подлинности**
  
  * Все пользователи, к которым применяется эта политика, должны будут проходить многофакторную проверку подлинности хотя бы один раз.
* **Требовать многофакторную проверку подлинности, если пользователь находится не на работе.**
  
  * Все пользователи, к которым применяется эта политика, должны будут проходить многофакторную проверку подлинности хотя бы один раз, если обращаются к службе не с рабочего удаленного расположения. Пользователи должны будут проходить многофакторную проверку подлинности, если обращаются к службе не с рабочего места.
* **Блокировать доступ, если пользователь находится не на работе.** 
  
  * При перемещении с рабочего места в удаленное расположение пользователи будут заблокированы, если к ним применяется политика "Блокировать доступ, если пользователь находится не на работе".  Оказавшись на рабочем месте, они вновь получат права на доступ.

## <a name="related-topics"></a>Связанные разделы
* [Защита доступа к Office 365 и другим приложениям, подключенным к Azure Active Directory](active-directory-conditional-access.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)


