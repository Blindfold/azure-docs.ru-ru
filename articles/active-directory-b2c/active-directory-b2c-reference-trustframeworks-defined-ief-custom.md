---
title: "Azure Active Directory B2C. Справка по инфраструктуре доверия | Документация Майкрософт"
description: "В этой статье описываются пользовательские политики Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 019dd66d5abe4242ffb6cdc3276929dcd7f04466
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="defining-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Определение инфраструктур доверия с помощью инфраструктуры процедур идентификации Azure AD B2C

Пользовательские политики Azure AD B2C, использующие инфраструктуру процедур идентификации, предоставляют организации централизованную службу, позволяющую упростить федерацию удостоверений в крупном, достойном внимания сообществе до одного отношения доверия и одной операции обмена метаданными.

Для этого требуются пользовательские политики Azure AD B2C, использующие инфраструктуру процедур идентификации, которые позволят вам найти ответы на следующие вопросы.

- *Какие юридические политики, политики безопасности, конфиденциальности и защиты данных необходимо соблюдать?*
- *С кем нужно связаться, чтобы пройти аккредитацию, и как проходит этот процесс?*
- *Кто является аккредитованными поставщиками сведений об удостоверениях (поставщики утверждений) и какие возможности они могут предложить?*
- *Кто является аккредитованной проверяющей стороной [и каковы ее требования (при необходимости)]?*
- *Каковы технические требования к сетевому взаимодействию для участников?*
- *Какие правила эксплуатации должны применяться к обмену информацией о цифровых удостоверениях?*

Чтобы ответить на эти вопросы, в пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, употребляется такой термин, как "инфраструктура доверия". Давайте рассмотрим этот термин и что вообще предоставляет инфраструктура доверия.

## <a name="understanding-the-trust-framework-and-federation-management-foundation"></a>Основные сведения об инфраструктуре доверия и основе управления федерацией

Инфраструктуру доверия следует воспринимать как письменную спецификацию с политиками удостоверений, безопасности, конфиденциальности и защите данных, которым должны следовать участники интересующего сообщества.

Федеративная идентификация позволяет обеспечить защиту удостоверений пользователей в Интернете.  При делегировании управления удостоверениями стороннему поставщику несколько проверяющих сторон могут использовать одно цифровое удостоверение для пользователя.  

Для защиты удостоверений поставщикам удостоверений и атрибутов действительно нужно соблюдать политики безопасности, конфиденциальности и эксплуатации, а также выполнять соответствующие рекомендации.  Если проверяющие стороны не могут выполнять прямые проверки, им нужно установить отношения доверия с выбранными поставщиками удостоверений и атрибутов.  Так как число получателей и поставщиков сведений о цифровых удостоверениях стремительно растет, продолжать обоюдное управление этими отношениями доверия или обмен техническими метаданными, требуемыми для подключения к сети, становится необоснованно.  Концентраторы федерации смогли решить эту проблему лишь частично.

Инфраструктуры доверия лежат в основе модели инфраструктуры доверия с открытым обменом удостоверениями (OIX), где каждое интересующее сообщество регулируется отдельной спецификацией инфраструктуры доверия, которая определяет следующее:

- Метрики безопасности и конфиденциальности для интересующего сообщества с такими определениями:
    - Уровни гарантии, которые предлагают или требуют участники, т. е. упорядоченный набор оценок достоверности, определяющих подлинность сведений о цифровых удостоверениях.
    - Уровни защиты, которые предлагают или требуют участники, т. е. упорядоченный набор оценок достоверности для обеспечения защиты сведений о цифровых удостоверениях, обрабатываемых участниками интересующего сообщества.
- Описание сведений о цифровых удостоверениях, которые предлагают или требуют участники.
- Технические политики для создания и использования сведений о цифровых удостоверениях, а также для определения уровня гарантии и защиты. Эти письменные политики обычно разделяются на следующие категории:
    - Политики доказательства идентичности. *Насколько строго проверяются сведения об удостоверении пользователя?*
    - Политики безопасности. *Каков уровень зашиты целостности и конфиденциальности данных?*
    - Политики конфиденциальности. *Может ли пользователь управлять персональными данными, позволяющими установить личность?*
    - Политики устойчивости. Непрерывная защита персональных данных, позволяющих установить личность, если поставщик прекращает работу.
- Технические профили для создания и использования сведений о цифровых удостоверениях. Эти профили требуются:
    - Для определения интерфейсов, для которых доступны сведения о цифровых удостоверениях на указанном уровне гарантии.
    - Для описания технических требований к сетевому взаимодействию.
- Описание различных ролей, которые могут выполнять участники сообщества, а также условия, которые нужно выполнить для работы с этими ролями.

Таким образом спецификация инфраструктуры доверия регулирует обмен данными об удостоверениях между участниками интересующего сообщества: проверяющими сторонами, поставщиками удостоверений и атрибутов и лицами, проверяющими атрибуты.

В терминах модели инфраструктуры доверия с открытым обменом удостоверениями спецификация инфраструктуры доверия состоит из одного или нескольких документов, с использованием которых осуществляется управление сообществом. Эти документы регулируют добавление и использование сведений о цифровых удостоверениях в сообществе. Это задокументированный набор политик и процедур, предназначенный для установления отношений доверия в цифровых удостоверениях, используемых членами сообщества в интерактивных операциях.  **Спецификация инфраструктуры доверия определяет правила создания приемлемой экосистемы федеративных удостоверений для сообщества.**

Сейчас множество специалистов сошлись во мнении о преимуществах такого подхода. Спецификации инфраструктуры доверия, несомненно, ускорят разработку экосистемы цифровых удостоверений с характеристиками безопасности, гарантии и конфиденциальности, которые можно проверить и, таким образом, использовать повторно в нескольких сообществах.

Именно поэтому в пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, спецификация применяется для представления данных для инфраструктуры доверия и упрощения взаимодействия.  

В пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, спецификация инфраструктуры доверия представлена как сочетание данных, используемых людьми и компьютерами: некоторые части этой модели (как правило, ориентированные на управление) представляются в виде ссылок на документацию по опубликованным политикам безопасности и конфиденциальности со связанными процедурами (при их наличии), а в других подробно описаны метаданные конфигурации и правила эксплуатации для упрощения автоматизации операций.

## <a name="understanding-trust-framework-policies"></a>Общие сведения о политиках инфраструктуры доверия

С точки зрения реализации спецификация инфраструктуры доверия, приведенная выше, в пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, состоит из набора политик, обеспечивающих полный контроль над работой с удостоверениями.  Пользовательские политики Azure AD B2C, использующие инфраструктуру процедур идентификации, действительно позволяют создавать свои инфраструктуры доверия через такие декларативные политики, с помощью которых можно определить и настроить следующее:

- Ссылки на документы, определяющие экосистему федеративных удостоверений сообщества, связанные с инфраструктурой доверия. Это ссылки на документацию по инфраструктуре доверия. Предопределенные правила эксплуатации или пути взаимодействия пользователя, автоматизирующие обмен утверждениями и их использование, а также управляющие этими процессами. Эти пути взаимодействия пользователя связаны с уровнем гарантии и защиты. Таким образом, в политике могут быть прописаны пути взаимодействия пользователя с разными уровнями гарантии и защиты.
- Поставщики удостоверений и атрибутов или поставщики утверждений в сообществе и технические профили, которые они поддерживают, а также аккредитация по внешним уровням гарантии и защиты, связанная с ними.
- Интеграция с лицами, проверяющими атрибуты, или с поставщиками утверждений.
- Проверяющие стороны в сообществе (за счет определения).
- Метаданные для сетевого подключения между участниками. Эти метаданные вместе с техническими профилями будут использоваться во время транзакции для настройки сетевого взаимодействия между проверяющей стороной и другими участниками сообщества.
- Преобразование протокола (если оно есть) (SAML, OAuth2, WS-Federation и OpenID Connect).
- Требования к проверке подлинности.
- Многофакторная оркестрация (если такая имеется).
- Общая схема для всех доступных утверждений и сопоставлений для участников сообщества.
- Все преобразования утверждений (вместе с возможной в этом контексте минимизацией данных) для поддержки обмена утверждениями и их использования.
- Привязка и шифрование.
- Хранилище утверждений.

> [!NOTE]
> Мы называем все возможные типы данных об удостоверениях, которыми можно обменяться, утверждениями (утверждения об учетных данных для проверки подлинности пользователя, проверке удостоверений, физическом расположении, атрибутах, позволяющих установить личность, и т. д.).  
>
> Мы используем термин *утверждения* (а не атрибуты, так как это подмножество), потому что при сетевых транзакциях проверяющие стороны не могут проверить их напрямую, как факты. Это скорее утверждения о фактах, в которых доверяющая сторона должна быть достаточно уверена, чтобы разрешить запрошенную транзакцию пользователя.  
>
> Кроме того, так происходит, потому что пользовательские политики Azure AD B2C, использующие инфраструктуру процедур идентификации, предназначены для упрощения согласованного обмена всеми типами сведений о цифровых удостоверениях независимо от того, для чего предназначен базовый протокол (проверки подлинности пользователя или извлечение атрибута).  Аналогичным образом мы используем термин "поставщики утверждений" для всех поставщиков удостоверений и атрибутов, а также для лиц, проверяющих атрибуты, когда не требуется различать конкретные функции.   

Таким образом они определяют способ обмена сведениями об удостоверениях между проверяющими сторонами, поставщиками удостоверений и атрибутов, а также лицами, проверяющими атрибуты. Кроме того, они позволяют определить поставщиков удостоверений и атрибутов, необходимых для проверки подлинности проверяющей стороны. Их следует рассматривать как доменный язык (DSL), то есть компьютерный язык, предназначенный для конкретного домена приложений с наследованием, инструкциями *IF*, полиморфизмом.

Эти политики входят в часть конструкции инфраструктуры доверия в пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, которую применяет компьютер, со всеми операционными сведениями, включая утверждения метаданных поставщиков и утверждения технических профилей с определением схем, утверждения преобразования функций и пути взаимодействия пользователя, указанные для упрощения оркестрации и автоматизации операций.  

Они считаются *динамическими документами* в пользовательских политиках Azure AD B2C, использующие инфраструктуру процедур идентификации, так как, вероятнее всего, их содержимое будет изменяться со временем в отношении активных участников, объявленных в политиках, а также, возможно, в некоторых ситуациях, связанных с условиями участия.  
Установка и обслуживание федерации существенно упрощается, так как проверяющим сторонам не нужно постоянно менять конфигурацию отношений доверия и подключения при изменении состава поставщиков утверждений и проверяющих в сообществе, представленном набором политик.

Еще одна нетривиальная задача — это взаимодействие. Дополнительных поставщиков утверждений и проверяющих нужно интегрировать, так как маловероятно, что проверяющие стороны поддерживают все необходимые протоколы. В пользовательских политиках Azure AD B2C, использующих инфраструктуру процедур идентификации, удалось решить эту проблему. Они поддерживают стандартные отраслевые протоколы и применяют конкретные пути взаимодействия пользователя с системой для транспонирования запросов, если проверяющие стороны и поставщики атрибутов не поддерживают те же протоколы.  

Пути взаимодействия пользователя с системой включают профили протоколов и метаданные, которые будут использоваться для настройки сетевого взаимодействия между проверяющей стороной и другими участниками.  Существуют также правила эксплуатации, которые будут применяться к сообщениям с запросами и ответами об обмене сведениями об удостоверениях, чтобы обеспечить соответствие требованиям опубликованных политик в рамках спецификации инфраструктуры доверия. Идея пути взаимодействия пользователя играет ключевую роль в настройке пользовательских возможностей и позволяет узнать о работе системы на уровне протокола.

На этой основе приложения проверяющей стороны и порталы в зависимости от контекста могут вызывать пользовательские политики Azure AD B2C, использующие инфраструктуру процедур идентификации, передав имя конкретной политики, и получить надежное и простое поведение и обмен информацией.

