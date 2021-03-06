---
title: "Пересылка данных задания службы автоматизации Azure в OMS Log Analytics | Документация Майкрософт"
description: "В этой статье показано, как отправлять состояние задания Runbook и потоки заданий в Log Analytics (Microsoft Operations Management Suite) для получения дополнительных сведений и возможностей управления."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2c25403a4bb2acd81061cc5dfdfd460c48a244bc
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Пересылка состояния задания и потоков заданий из службы автоматизации в Log Analytics (OMS)
Служба автоматизации может отправлять состояние задания Runbook и потоки заданий в рабочую область Log Analytics в Microsoft Operations Management Suite (OMS).  На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. С помощью Log Analytics теперь можно:

* получить информацию о заданиях службы автоматизации;
* активировать отправку электронного сообщения или оповещения в соответствии с состоянием задания Runbook (например, сбой или приостановка);
* создавать сложные запросы для потоков заданий;
* коррелировать задания и учетные записи службы автоматизации;
* визуализировать журнал задания по прошествии времени.     

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию
Чтобы начать отправку журналов службы автоматизации в Log Analytics, необходимо следующее.

1. Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](/powershell/azure/overview) (вер. 2.3.0).
2. Рабочая область Log Analytics. Дополнительные сведения см. в статье [Начало работы с Log Analytics](../log-analytics/log-analytics-get-started.md).
3. ResourceId для учетной записи службы автоматизации Azure

Чтобы узнать ResourceId для учетной записи службы автоматизации Azure и рабочей области Log Analytics, выполните следующую команду PowerShell.

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если у вас несколько учетных записей службы автоматизации или рабочих областей, в выходных данных предыдущей команды найдите нужное значение *Name* и скопируйте соответствующее значение *ResourceId*.

Если вам нужно найти значение *Name* для своей учетной записи службы автоматизации, на портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**.  В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br> ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Настройка интеграции с Log Analytics
1. На своем компьютере запустите **Windows PowerShell** на **начальном** экране.  
2. Скопируйте и вставьте приведенную ниже команду PowerShell и измените значение `$workspaceId` и `$automationAccountId`.  Допустимыми значениями параметра `-Environment` являются *AzureCloud* и *AzureUSGovernment*. Его значение зависит от того, в какой облачной среде вы работаете.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
            [ValidateSet("AzureCloud","AzureUSGovernment")]
            [string]$Environment="AzureCloud",
        [Parameter(Mandatory=$True)]
        [string]$nameOfYourLogAnalyticsWorkspace,
        [Parameter(Mandatory=$True)]
        [string]$nameOfYourAutomationAccount
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }

# Below both the ResourceId's are populated using the Parameters from this script as a searchstring based on the Name of the Automation Account and Name of Workspace

$workspaceId=(Get-AzureRmOperationalInsightsWorkspace|where Name -like ('*'+$nameOfYourLogAnalyticsWorkspace+'*')).ResourceId
$automationAccountId=(Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts" -ResourceNameContains $nameOfYourAutomationAccount).Resourceid

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

После запуска этого сценария вы увидите записи в Log Analytics, добавленные в новый элемент JobLogs или JobStreams за 10 минут.

Чтобы просмотреть журналы, выполните следующий запрос: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Проверка конфигурации
Чтобы убедиться, что ваша учетная запись службы автоматизации отправляет журналы в рабочую область Log Analytics, проверьте правильность настройки диагностики в учетной записи службы автоматизации, используя следующую команду PowerShell.

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
            [ValidateSet("AzureCloud","AzureUSGovernment")]
            [string]$Environment="AzureCloud",
        [Parameter(Mandatory=$True)]
        [string]$nameOfYourAutomationAccount
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }

# Below the ResourceId is populated using the Parameter from this script as a searchstring based on the Name of the Workspace

$automationAccountId=(Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts" -ResourceNameContains $nameOfYourAutomationAccount).Resourceid

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных убедитесь в следующем.
+ В разделе *Logs* значение *Enabled* равно *True*.
+ *WorkspaceId* присвоено значение ResourceId рабочей области Log Analytics.


## <a name="log-analytics-records"></a>Записи Log Analytics
При диагностике из службы автоматизации Azure в Log Analytics создаются записи двух типов.

### <a name="job-logs"></a>Журналы заданий
| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию.  Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook.  Возможные значения:<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Категория | Классификация типа данных.  Для службы автоматизации значением является JobLogs. |
| OperationName | Указывает тип операции, выполняемой в Azure.  Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Описывает состояние результата задания Runbook.  Возможные значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Потоки заданий
| Свойство | Описание |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию.  Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможные значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook.  Возможные значения:<br>- In Progress |
| Категория | Классификация типа данных.  Для службы автоматизации значением является JobStreams. |
| OperationName | Указывает тип операции, выполняемой в Azure.  Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Включает в себя выходной поток из Runbook. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Просмотр журналов службы автоматизации в Log Analytics
Теперь, когда вы начали отправку журналов заданий службы автоматизации в Log Analytics, давайте узнаем, что с ними можно сделать в Log Analytics.

Чтобы просмотреть журналы, выполните следующий запрос: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook
Один из наших основных клиентов запрашивает возможность отправлять электронное сообщение или текст при возникновении ошибки в работе задания Runbook.   

Чтобы создать правило генерации оповещений, начните с создания поиска в журнале записей заданий Runbook, которые должны вызывать оповещение.  Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора Log Analytics щелкните **Поиск по журналу**.
2. Создайте запрос на поиска оповещения в журнале. Для этого в поле запроса введите следующее условие поиска: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)`. Можно также применить группирование по RunbookName с помощью: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации.  Имя учетной записи службы автоматизации можно получить из поля "Ресурс" для поиска JobLogs.  
3. Чтобы открыть экран **Добавить правило оповещения**, щелкните **Оповещение** в верхней части страницы. Дополнительные сведения о параметрах для настройки оповещения см. в разделе [Оповещения в Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками
Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В этих случаях PowerShell создает поток сообщений об ошибках, но устранимые ошибки не приводят к приостановке или сбою задания.    

1. В рабочей области Log Analytics щелкните **Поиск по журналу**.
2. В поле запроса введите `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` и нажмите кнопку **Поиск**.

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания
При отладке задания можно также просмотреть потоки заданий.  Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания
Наконец, вам может понадобиться визуализировать журнал задания по прошествии времени.  Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![Диаграмма хронологии состояния задания в OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Сводка
Отправляя состояние задания службы автоматизации и поток данных в Log Analytics, можно лучше понять, в каком состоянии находятся ваши задания службы автоматизации. Для этого вы можете:
+ настроить оповещения, уведомляющие вас о проблемах;
+ с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  

Log Analytics предоставляет больший оперативный контроль над заданиями службы автоматизации и позволяет быстрее устранять инциденты.  

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы узнать больше о том, как создавать различные поисковые запросы и просматривать журналы заданий службы автоматизации с помощью Log Analytics, ознакомьтесь с разделом [Поиск по журналам в Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Чтобы понять, как создавать и извлекать выходные данные и сообщения об ошибках из модулей Runbook, ознакомьтесь с разделом [Выходные данные и сообщения Runbook в службе автоматизации Azure](automation-runbook-output-and-messages.md)
* Чтобы узнать больше о выполнении модулей Runbook, отслеживании заданий Runbook и других технических деталях, ознакомьтесь с [отслеживанием задания Runbook](automation-runbook-execution.md)
* Чтобы узнать больше о Log Analytics (OMS) и источниках сбора данных, ознакомьтесь с разделом [Подключение службы Azure к Log Analytics](../log-analytics/log-analytics-azure-storage.md)

