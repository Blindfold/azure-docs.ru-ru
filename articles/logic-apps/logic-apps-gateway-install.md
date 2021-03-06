---
title: "Установка локального шлюза данных в Azure Logic Apps | Документация Майкрософт"
description: "Доступ к локальным данным из приложений логики с помощью установки локального шлюза данных"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b9971117d5f61669a5161a28c96b11b2fd600b61
ms.lasthandoff: 04/14/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Установка локального шлюза данных для Azure Logic Apps

Локальный шлюз данных поддерживает следующие подключения:

*   BizTalk Server
*   DB2  
*   Файловая система
*   Informix
*   Магический квадрант
*   MySQL
*   База данных Oracle 
*   сервер приложений SAP; 
*   сервер сообщений SAP;
*   SharePoint только для HTTP (не для HTTPS)
*   SQL Server
*   Teradata

Дополнительные сведения о подключениях, приведенных выше, см. в статье [Список соединителей](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="installation-and-configuration"></a>Установка и настройка

### <a name="requirements"></a>Требования

Минимальные:

* .NET Framework 4.5;
* 64-разрядная версия Windows 7 или Windows Server 2008 R2 (или более поздняя версия).

Рекомендация:

* 8-ядерный ЦП;
* 8 ГБ ОЗУ;
* 64-разрядная версия Windows 2012 R2 (или более поздняя версия).

Сопутствующие рекомендации:

* Локальный шлюз данных должен быть установлен только на локальном компьютере.
Нельзя устанавливать его на контроллере домена.

* Не стоит устанавливать шлюз на компьютере, который может выключиться, перейти в спящий режим или не сможет подключиться к Интернету. Все эти обстоятельства помешают выполнению шлюза. Кроме того, может наблюдаться снижение производительности шлюза при использовании беспроводной сети.

* Чтобы связать локальный шлюз данных с вашей учетной записью на основе Azure Active Directory, вы можете использовать только рабочий или учебный электронный адрес в Azure.

    Если вы используете учетную запись Майкрософт, например @outlook.com, вы можете использовать учетную запись Azure для   [создания рабочего или учебного электронного адреса](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

### <a name="install-the-gateway"></a>Установка шлюза

1.    Скачайте установщик локального шлюза данных [здесь](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2.    Выберите **локальный шлюз данных** в качестве режима.

3. Выполните вход с помощью рабочей или учебной учетной записи Azure. 

4. Настройте новый шлюз либо перенесите, восстановите или перехватите существующий.

    Чтобы настроить шлюз, укажите имя шлюза и ключ восстановления, а затем выберите **Настроить**.
  
    Укажите ключ восстановления, который содержит как минимум 8 знаков, и сохраните его в надежном месте. Этот ключ понадобится, если вы захотите перенести, восстановить или перехватить связанный с ним шлюз.

    Чтобы перенести, восстановить или перехватить существующий шлюз, укажите ключ восстановления, который был указан при создании этого шлюза.

### <a name="restart-the-gateway"></a>Перезапуск шлюза

Шлюз работает как служба Windows и, как и любая другая служба Windows, может быть запущен и остановлен различными способами. Например, можно открыть командную строку с дополнительными разрешениями на компьютере, на котором выполняется шлюз, и выполнить одну из команд, приведенных ниже.

* Чтобы остановить службу, выполните указанную ниже команду.
  
    `net stop PBIEgwService`

* Чтобы запустить службу, выполните указанную ниже команду.
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Настройка брандмауэра или прокси-сервера

Сведения о том, как предоставить данные прокси-сервера для шлюза, см. в статье [Настройка параметров прокси-сервера для локального шлюза данных](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Проверить, не блокирует ли брандмауэр или прокси-сервер подключения, можно, выполнив приведенную ниже команду в командной строке PowerShell. Эта команда проверяет возможность подключения к служебной шине Azure (только сетевое подключение), поэтому она никак не влияет на службу облачного сервера или шлюз. Эта проверка помогает определить, может ли ваш компьютер в данный момент подключиться к Интернету.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Результаты должны выглядеть примерно как в приведенном примере. Если значение **TcpTestSucceeded** не true, то, возможно, подключения блокируются брандмауэром.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Чтобы обеспечить исчерпывающие сведения, замените значения параметров **ComputerName** и **Port** значениями, приведенными ниже в разделе [Настройка портов](#configure-ports).

Брандмауэр может также блокировать подключения, устанавливаемые служебной шиной Azure с центрами обработки данных Azure. В этом случае утвердите (разблокируйте) все IP-адреса для этих центров обработки данных в вашем регионе.
Список IP-адресов Azure можно получить [здесь](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Настройка портов
Шлюз создает исходящее подключение к служебной шине Azure и осуществляет связь через исходящие порты: TCP 443 (по умолчанию), 5671, 5672 и порты 9350–9354. Шлюзу не требуются входящие порты.

Вы можете больше узнать о [гибридных решениях](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| ДОМЕННЫЕ ИМЕНА | ИСХОДЯЩИЕ ПОРТЫ | ОПИСАНИЕ |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671–5672 | Протокол AMQP | 
| *.servicebus.windows.net | 443, 9350–9354 | Прослушиватели ретрансляции служебной шины по протоколу TCP (порт 443 требуется для получения маркера контроля доступа). | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Используется для проверки подключения к Интернету, если шлюз недоступен для службы Power BI. | 

Если требуется утвердить IP-адреса, а не домены, то можно скачать и использовать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). В некоторых случаях подключения к служебной шине Azure будут устанавливаться по IP-адресу, а не полному доменному имени.

### <a name="sign-in-accounts"></a>Учетные записи для входа

Вы можете выполнить вход с помощью рабочей или учебной учетной записи, являющейся учетной записью организации. Если вы зарегистрировались для использования предложения Office 365 и не предоставили действительный рабочий электронный адрес, то ваш адрес входа может выглядеть так: jeff@contoso.onmicrosoft.com. В облачной службе ваша учетная запись хранится в клиенте в Azure Active Directory (Azure AD). Обычно имя участника-пользователя учетной записи Azure AD совпадает с адресом электронной почты.

### <a name="windows-service-account"></a>Учетная запись службы Windows

Локальный шлюз данных настроен для использования NT SERVICE\PBIEgwService в качестве учетных данных входа службы Windows. По умолчанию шлюзу назначено право "входа в качестве службы" в контексте компьютера, на котором он установлен.

Эта учетная запись службы не является ни учетной записью, которая использовалась для подключения к локальным источникам данных, ни рабочей или учебной учетной записью, используемой для входа в облачные службы.

## <a name="how-the-gateway-works"></a>Как работает шлюз
Когда пользователи взаимодействуют с элементом, подключенным к локальному источнику данных, происходит следующее:

1. Облачная служба создает запрос, а также зашифрованные учетные данные для источника данных, и отправляет запрос в очередь для обработки шлюзом.
2. Служба анализирует запрос и отправляет его в служебную шину Azure.
3. Локальный шлюз данных опрашивает служебную шину Azure, чтобы проверить наличие ожидающих запросов.
4. Шлюз получает запрос, расшифровывает учетные данные и подключается к источникам данных, используя эти учетные данные.
5. Затем шлюз отправляет запрос к источнику данных для выполнения.
6. Результаты отправляются из источника данных обратно в шлюз, а затем — в облачную службу. Затем служба использует эти результаты.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="general"></a>Общие сведения

**Вопрос**. Нужно ли устанавливать шлюз для источников данных в облаке, например SQL Azure? <br/>
**Ответ**. Нет. Шлюз подключается только к локальным источникам данных.

**Вопрос**. Как на самом деле называется служба Windows?<br/>
**Ответ**. В списке служб шлюз называется "Служба Power BI Enterprise Gateway".

**Вопрос**. Устанавливаются ли какие-либо входящие подключения к шлюзу из облака? <br/>
**Ответ**. Нет. Шлюз использует исходящие подключения к служебной шине Azure.

**Вопрос**. Что делать, если мной заблокированы исходящие подключения? Что нужно открыть? <br/>
**Ответ**. Проверьте порты и узлы, которые использует шлюз.

**Вопрос**. Нужно ли устанавливать шлюз на том же компьютере, на котором находится источник данных? <br/>
**Ответ**. Нет. Шлюз подключается к источнику данных, используя предоставленные сведения о подключении. В этом смысле шлюз можно рассматривать как клиентское приложение. Ему просто необходима возможность подключения к серверу, имя которого было предоставлено.

**Вопрос**. Что такое задержка при выполнении запросов к источнику данных из шлюза? Какая архитектура подойдет лучше всего? <br/>
**Ответ**. Чтобы уменьшить задержки в сети, необходимо установить шлюз как можно ближе к источнику данных. Вы можете установить шлюз на фактический источник данных, что сведет к минимуму соответствующие задержки. Стоит также рассмотреть использование центров обработки данных. Например, если служба использует центр обработки данных в западной части США, а SQL Server размещен на виртуальной машине Azure, то эту виртуальную машину лучше также разместить в западной части США. Это позволит свести к минимуму задержки и избежать затрат на исходящий трафик виртуальной машины Azure.

**Вопрос**. Есть ли какие-либо требования к пропускной способности сети? <br/>
**Ответ.** Пропускная способность сети должна быть высокой. Все среды разные и объем отправляемых данных влияет на результаты. Гарантировать определенный уровень пропускной способности между локальной средой и центрами обработки данных Azure может помочь применение ExpressRoute.

Измерить текущую пропускную способность можно с помощью стороннего инструмента — приложения Azure Speed Test.

**Вопрос**. Может ли служба Windows шлюза работать с учетной записью Azure Active Directory? <br/>
**Ответ**. Нет. Службе Windows требуется действительная учетная запись Windows. По умолчанию служба будет выполняться с идентификатором безопасности службы NT SERVICE\PBIEgwService.

**Вопрос**. Как результаты отправляются обратно в облако? <br/>
**Ответ.** Результаты отправляются с помощью служебной шины Azure.

**Вопрос**. Где хранятся мои учетные данные? <br/>
**Ответ.** Учетные данные, введенные для источника данных, хранятся в облачной службе шлюза в зашифрованном виде. Расшифровываются они в локальном шлюзе.

### <a name="high-availabilitydisaster-recovery"></a>Высокий уровень доступности и аварийное восстановление
**Вопрос**. Существуют ли какие-либо планы по реализации сценариев высокого уровня доступности с помощью шлюза? <br/>
**Ответ.** Это входит в наши планы, однако со сроками в этом отношении мы еще не определились.

**Вопрос**. Какие варианты доступны для аварийного восстановления? <br/>
**Ответ**. Вы можете использовать ключ восстановления для восстановления или переноса шлюза. Ключ восстановления указывается при установке шлюза.

**Вопрос**. В чем заключается преимущество ключа восстановления? <br/>
**Ответ.** Он позволяет перенести или восстановить параметры шлюза после аварии.

## <a name="troubleshooting"></a>Устранение неполадок

**Вопрос**. Где находятся журналы шлюза? <br/>
**Ответ**. См. раздел "Средства" далее в этой статье.

**Вопрос**. Как посмотреть, какие запросы отправляются к локальному источнику данных? <br/>
**Ответ.** Вы можете включить трассировку запросов, в том числе и отправляемых. Не забудьте вернуть исходное значение трассировки запросов после устранения неполадок. При включенной трассировке запросов создаются журналы большего размера.

Кроме того, можно рассмотреть инструменты трассировки запросов, доступные для вашего источника данных. Например, можно использовать расширенные события или SQL Profiler для SQL Server и служб Analysis Services.

### <a name="update-to-the-latest-version"></a>Обновление до последней версии

При использовании устаревшей версии шлюза может возникать ряд проблем. Рекомендуем убедиться, что используется последняя версия. Если шлюз не обновлялся месяц или дольше, то можно установить его последнюю версию и проверить, получится ли воспроизвести проблему.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Error: Failed to add user to group. (Ошибка: не удалось добавить пользователя в группу.) (-2147463168 PBIEgwService Performance Log Users ) (-2147463168 пользователей журналов производительности PBIEgwService)

Эта ошибка появляется, если вы пытаетесь установить шлюз на контроллер домена, который не поддерживается. Убедитесь, что развертывание шлюза происходит на компьютере, который не является контроллером домена.

## <a name="tools"></a>Средства
### <a name="collect-logs-from-the-gateway-configurer"></a>Сбор журналов из конфигуратора шлюза

Вы можете собирать несколько журналов для шлюза. Всегда начинайте с журналов!

#### <a name="installer-logs"></a>Журналы установщика

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Журналы конфигурации

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Журналы службы корпоративного шлюза

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Журналы событий

Журналы шлюза управления данными и PowerBIGateway находятся в разделе **Журналы приложения и служб**.

### <a name="fiddler-trace"></a>Трассировки Fiddler

[Fiddler](http://www.telerik.com/fiddler) — это бесплатный инструмент от компании Telerik, который отслеживает трафик HTTP. Вы можете просматривать данные с помощью службы Power BI с клиентского компьютера. Эта служба позволит выявить ошибки и другие сопутствующие сведения.

## <a name="next-steps"></a>Дальнейшие действия
    
* [Подключение к локальным данным из приложений логики](../logic-apps/logic-apps-gateway-connection.md)
* [Возможности интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Список соединителей](../connectors/apis-list.md)

