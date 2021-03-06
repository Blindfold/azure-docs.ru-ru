---
title: "Управление веб-приложением в службе приложений Azure"
description: "Ссылки на ресурсы, посвященные управлению веб-приложением в службе приложений Azure."
services: app-service\web
documentationcenter: 
author: erikre
manager: erikre
editor: 
ms.assetid: d5e2887a-84f9-4747-a573-867635cb8b39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 50630084a3df9bc1fed27efb41bc557d0e03916f
ms.lasthandoff: 12/06/2016


---
# <a name="manage-a-web-app-in-azure-app-service"></a>Управление веб-приложением в службе приложений Azure
В этом разделе представлены ссылки на ресурсы, посвященные управлению веб-приложением в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Под управлением подразумеваются все задачи, направленные на обеспечение стабильной работы веб-приложения. 

На различных этапах существования веб-приложения вам придется выполнять разные задачи управления, от первоначального развертывания до обычной эксплуатации, обслуживания и применения обновлений.

Многие задачи, связанные с управлением веб-приложением, можно выполнять на портале Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Перед развертыванием веб-приложения в рабочей среде
### <a name="choose-a-tier"></a>Выберите уровень.
Служба приложений Azure доступна для 5 уровней: Free, Shared, Basic, Standard и Premium. Информацию о функциях и ценах для каждого уровня см. в разделе [Сведения о ценах](https://azure.microsoft.com/pricing/details/app-service/). 

* [Планы службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) позволяют группировать несколько веб-приложений на одном уровне.
* Всегда можно [сменить уровень](web-sites-scale.md) после создания веб-приложения.

### <a name="configuration"></a>Конфигурация
На [портале Azure](https://portal.azure.com/) можно задать различные параметры конфигурации. Подробные сведения см. в разделе [Настройка веб-приложений в службе приложений Azure](web-sites-configure.md). Вот краткий контрольный список:

* При необходимости выберите **версии сред выполнения** для .NET, PHP, Java или Python.
* Если в веб-приложении используется протокол **WebSockets**, включите его поддержку. (Это относится к приложениям, которые используют [ASP.NET SignalR](http://www.asp.net/signalr) или [socket.io](web-sites-nodejs-chat-app-socketio.md).)
* Вы используете непрерывные веб-задания? Если да, то выберите параметр **Always On**(Всегда включено).
* Задайте **документ по умолчанию**, например index.html.

Помимо этих базовых параметров конфигурации, вы также можете настроить следующее:

* Шифрование **SSL**. Чтобы использовать SSL с пользовательским доменным именем, вам необходимо получить SSL-сертификат и настроить веб-приложение для работы с ним. Ознакомьтесь со статьей [Включение протокола HTTPS для веб-приложения в службе приложений Azure](web-sites-configure-ssl-certificate.md).
* **Имя личного домена**. Вашему веб-приложению автоматически присваивается дочерний домен в azurewebsites.net. Вы можете связать сайт с пользовательским доменным именем, например contoso.com. Ознакомьтесь со статьей [Настройка личного доменного имени для службы приложений Azure](web-sites-custom-domain-name.md).

Настройка для определенных языков программирования:

* **PHP**: статья [Настройка PHP в веб-приложениях службы приложений Azure](web-sites-php-configure.md).
* **Python**: статья [Настройка Python в веб-приложениях службы приложений Azure](web-sites-python-configure.md)

## <a name="while-your-web-app-is-running"></a>В ходе эксплуатации веб-приложения
В ходе эксплуатации веб-приложения важно, чтобы оно всегда было доступно, а его ресурсы масштабировались с учетом пользовательского трафика. Кроме того, возможно, вам придется устранять возникающие неполадки.

### <a name="monitoring"></a>Мониторинг
* На портале Azure можно [добавить показатели производительности](web-sites-monitor.md) , например показатель использования ЦП и количество запросов от клиентов.
* [Ресурсы веб-сайта можно масштабировать](web-sites-scale.md) с учетом трафика. В зависимости от уровня плана размещения можно изменять количество виртуальных машин и (или) размер экземпляров виртуальных машин. На уровнях Standard и Premium вы также можете настроить автоматическое масштабирование веб-приложения по заданному графику или с учетом нагрузки.  

### <a name="backups"></a>Резервные копии
* Настройка [автоматизированной архивации](web-sites-backup.md) для веб-приложения. Дополнительные сведения об архивации см. в [этом видео](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
* Ознакомьтесь с параметрами [восстановления базы данных](../sql-database/sql-database-business-continuity.md) SQL Azure.

### <a name="troubleshooting"></a>Устранение неполадок
* Возникающие неполадки можно [устранять в Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)с помощью журналов диагностики и отладки в облаке в режиме реального времени. 
* Кроме того, журналы диагностики можно собирать различными способами не только в Visual Studio. Ознакомьтесь со статьей [Включение ведения журнала диагностики для веб-приложений в службе приложений Azure](web-sites-enable-diagnostic-log.md).
* Работа с приложениями Node.js описана в разделе [Отладка веб-приложения Node.js в службе приложений Azure](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Восстановление данных
* [восстанавливать](web-sites-restore.md) из ранее созданных резервных копий.

## <a name="when-you-update-your-web-app"></a>При обновлении веб-приложения
Если вы не включили автоматическое резервное копирование, то можете создавать резервные копии [вручную](web-sites-backup.md).

Возможно, вас также заинтересует [промежуточное развертывание](web-sites-staged-publishing.md). С его помощью вы сможете публиковать обновления в промежуточной среде, функционирующей параллельно с рабочей. 

Если вы используете Visual Studio Team Services, то можете настроить непрерывное развертывание из системы управления версиями:

* [С помощью системы управления версиями Team Foundation](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
* [С помощью Git](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)

<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website



