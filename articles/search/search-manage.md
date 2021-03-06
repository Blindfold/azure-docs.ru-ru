---
title: "Администрирование службы поиска Azure на портале Azure"
description: "Узнайте, как управлять Поиском Azure, размещенной в Microsoft Azure облачной службой поиска, с помощью портала Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/05/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: ab914153df01c6d8135732bc772b78066e14d1d1
ms.lasthandoff: 04/07/2017


---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Администрирование службы поиска Azure на портале Azure
> [!div class="op_single_selector"]
> * [Портал](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Поиск Azure — это облачная служба поиска с полным управлением, которая используется для создания многофункционального поискового интерфейса в пользовательских приложениях. В этой статье описываются задачи *администрирования службы* , которые можно выполнять на [портале Azure](https://portal.azure.com) в отношении службы поиска, которая уже была подготовлена. *Администрирование службы* имеет упрощенный дизайн, который ограничивается следующими задачами:

* управление *ключами API* , используемыми в службе для операций чтения и записи, а также обеспечение безопасного доступа к ним;
* регулирование емкости службы за счет изменения выделения секций и реплик;
* отслеживание использования ресурсов относительно ограничений ценовой категории службы.

**За рамками данной статьи** 

*Управление содержимым* (или управление индексами) подразумевает операции, такие как анализ поискового трафика для определения объема запросов, обнаружение терминов, которые ищут пользователи, а также выявление успешности поиска (позволяют ли результаты поиска привести пользователей к конкретным документам в вашем индексе). Управление содержимым выходит за рамки данной статьи. Более глубокое понимание внутренних операций на уровне индексов описано в разделе [Включение и использование аналитики поискового трафика](search-traffic-analytics.md).

*Производительность запросов* также выходит за рамки данной статьи. Дополнительные сведения см. в статьях [Контроль использования и статистики в службе поиска Azure](search-monitor-usage.md) и [Рекомендации по производительности и оптимизации Поиска Azure](search-performance-optimization.md).


<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Права администратора
Подготовку или выведение из эксплуатации самой службы могут выполнять администраторы подписки Azure или соадминистраторы.

В пределах службы любой пользователь с доступом к URL-адресу службы и ключом API администратора имеет доступ к службе для чтения и записи, а следовательно имеет возможность добавлять, удалять и изменять объекты сервера, такие как ключи API, индексы, индексаторы, источники данных, расписания и назначения ролей, реализованные посредством [управления доступом на основе ролей (RBAC)](#rbac).

Все взаимодействия пользователя со службой поиска Azure относятся к одному из следующих режимов: доступ к службе для чтения и записи (права администратора) или доступ к службе только для чтения (права запроса). Дополнительные сведения см. в разделе [Управление ключами API](#manage-keys).

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>Настройка ролей RBAC с правами администратора
Azure реализует [глобальную модель авторизации на основе ролей](../active-directory/role-based-access-control-configure.md) для всех служб, управляемых через портал или API Resource Manager. Роли владельца, участника или читателя определяют уровень администрирования службы для пользователей, групп и субъектов безопасности Active Directory, назначенных для каждой роли. 

Для службы поиска Azure разрешения RBAC определяют следующие задачи администрирования:

| Роль | Задача |
| --- | --- |
| Владелец |Создание или удаление службы или любого объекта в службе, включая ключи API, индексы, индексаторы, источники данных индексаторов и расписания индексаторов.<p>Просмотр состояния службы, в том числе счетчиков и размера хранилища.<p>Добавление или удаление ролевой принадлежности (только владелец может управлять ролевой принадлежностью).<p>Администраторам подписки и владельцам службы автоматически присваивается роль владельца. |
| Участник |Тот же уровень доступа, что и владелец, но без возможности управлять ролями. Например, участник может просматривать и повторно создавать `api-key`, но не может изменять членство в ролях. |
| читатель. |Просматривает состояние службы и ключи запросов. Пользователи в этой роли не могут изменять конфигурацию службы, а также не могут просматривать ключи администратора. |

Обратите внимание, что роли не гарантируют право доступа к конечной точке службы. Операции службы поиска, такие как управление индексами, совокупностями индексов и поисковые запросы к данным, управляются посредством ключей API, а не через роли. Дополнительные сведения см. в разделе "Предоставление прав на управление и операции с данными" статьи [Начало работы с управлением доступом на портале Azure](../active-directory/role-based-access-control-what-is.md).

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>Ведение журнала и сведения о системе
Поиск Azure не предоставляет файлы журналов для отдельных служб ни через портал, ни через программные интерфейсы. На уровне "Базовый" и выше корпорация Майкрософт отслеживает все службы поиска Azure с целью обеспечения доступности не менее 99,9 %, в соответствии с соглашениями об уровне обслуживания (SLA). Если служба работает медленно, или пропускная способность опускается ниже пороговых значений, предусмотренных соглашением об уровне обслуживания, то группы поддержки изучают доступные файлы журналов и пытаются устранить проблему.

Общие сведения о службе можно получить следующими способами:

* на портале на панели мониторинга службы посредством уведомлений, свойств и сообщений о состоянии;
* с помощью [PowerShell](search-manage-powershell.md) или [REST API управления](https://docs.microsoft.com/rest/api/searchmanagement/) для [получения свойств службы](https://docs.microsoft.com/rest/api/searchmanagement/services) или сведений об уровне использования ресурсов индексом;
* с помощью функции [аналитики поискового трафика](search-traffic-analytics.md), как уже говорилось ранее.

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>Управление ключами API
Для всех запросов к службе поиска требуется ключ API, который был создан специально для службы. Этот ключ API является единственным механизмом аутентификации доступа к конечной точке службы поиска. 

Ключ API является строкой, состоящей из случайно сгенерированных букв и цифр. Он создается исключительно службой. Используя [разрешения RBAC](#rbac), можно удалить или прочитать ключи, но нельзя переопределить созданный ключ с помощью определенной пользователем строки (в частности, если у вас есть регулярно используемые пароли, то ключ API нельзя заменить определенным пользователем паролем). 

Для доступа к вашей службе поиска используются два типа ключей:

* ключ администратора (применяется к любым операциям чтения и записи в отношении службы);
* ключ запроса (применяется только к операциям чтения, таким как запросы к индексу).

Ключ API администратора создается в процессе подготовки службы. Существует два ключа администратора, обозначенные для упорядочения как *первичный* и *вторичный*, хотя в действительности они являются взаимозаменяемыми. У каждой службы есть два ключа администратора, чтобы вы могли сменять их, не теряя доступа к службе. Вы можете восстановить любой ключ администратора, но вы не сможете добавить его к общему количеству ключей администратора. На одну службу поиска допускается не более двух ключей администратора.

Ключи запроса предназначены для клиентских приложений, которые могут напрямую вызывать поиск. Вы можете создать до 50 ключей поисковых запросов. В коде приложения указывается URL-адрес поиска и ключ API запроса, чтобы разрешить доступ к службе только для чтения. Код приложения также указывает индекс, используемый приложением. Используемые вместе конечная точка, ключ API с доступом только для чтения и целевой индекс определяют область применения и уровень доступа для подключения из клиентского приложения.

Для получения или повторного создания ключей API необходимо открыть панель мониторинга. Нажмите **КЛЮЧИ** , чтобы открыть страницу управления ключами. В верхней части страницы находятся команды для создания или повторного создания ключей. По умолчанию создаются только ключи администратора. Ключи API запроса необходимо создать вручную.

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>Обеспечение безопасности ключей API
Безопасность ключей API обеспечивается за счет ограничения доступа на портале или в интерфейсах Resource Manager (PowerShell или интерфейс командной строки). Как уже упоминалось, администраторы подписки могут просматривать и повторно создавать все ключи API. В качестве меры предосторожности проверьте назначения ролей, чтобы понять, кто имеет доступ к ключам администратора.

1. На панели мониторинга службы щелкните значок "Доступ", чтобы открыть колонку "Пользователи".
   ![][7]
2. В колонке "Пользователи" проверьте существующие назначения ролей. Как и ожидалось, администраторы подписки уже имеют полный доступ к службе через роль владельца.
3. Для дальнейшей детализации щелкните **Администраторы подписки** и разверните список назначения ролей, чтобы просмотреть, кто имеет права соадминистратора службы поиска.

Другой способ просмотреть права доступа — это выбрать пункт **Роли** в колонке "Пользователи". При этом отобразятся доступные роли и количество пользователей или групп, назначенных каждой роли.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Отслеживание использования ресурсов
На панели мониторинга отслеживание ресурсов ограничивается информацией, показанной на панели мониторинга службы, а также несколькими метриками, которые вы можете получить с помощью запроса к службе. В панели мониторинга службы, в разделе «Использование», вы можете быстро определить, являются ли уровни ресурсов раздела достаточными для вашего приложения.

С помощью службы интерфейса API службы поиска вы можете рассчитать количество документов и индексов. Существуют жесткие ограничения, связанные с этими количествами на основе уровня ценообразования. Дополнительные сведения см. в статье [Ограничения поиска Azure](search-limits-quotas-capacity.md). 

* [Получение статистики индексов](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Подсчет документов](https://docs.microsoft.com/rest/api/searchservice/count-documents)

> [!NOTE]
> Поведение кэша может временно превышать предел. Например, при использовании общей службы вы можете увидеть количество документов, превышающее жесткий предел в 10000 документов. Такое преувеличение является временным и будет обнаружено при следующей проверке лимитов. 
> 
> 

## <a name="disaster-recovery-and-service-outages"></a>Аварийное восстановление и простои службы

Данные можно восстановить, по Поиск Azure не обеспечивает немедленную отработку отказа службы, если происходит сбой на уровне кластера или центра обработки данных. Если сбой кластера произойдет в центре обработки данных, то рабочая группа обнаружит проблему и сделает все возможное для восстановления службы. Во время восстановления службы возникнет простой. Вы можете запросить компенсацию кредитов за недоступность службы в соответствии с условиями [Соглашения об уровне обслуживания (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Чтобы обеспечить непрерывное обслуживание, включая критические сбои, не контролируемые корпорацией Майкрософт, необходимо выполнить [подготовку дополнительной службы](search-create-service-portal.md) в другом регионе и реализовать стратегию георепликации, чтобы обеспечить полную избыточность индексов для всех служб.

Пользователи, использующие индексаторы для заполнения и обновления индексов, выполняют аварийное восстановление с помощью индексаторов в определенном географическом регионе, используя один источник данных. Вместо индексаторов будет использоваться код приложения, одновременно передающий объекты и данные в различные службы. Дополнительные сведения см. в статье [Рекомендации по производительности и оптимизации Поиска Azure](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Архивация и восстановление

Так как Поиск Azure не является основным решением для хранения данных, мы не предоставляем формальный механизм для автоматического резервного копирования и самостоятельного восстановления. Код приложения, используемый для создания и заполнения индекса, де-факто является возможностью восстановления на случай, если индекс удаляется по ошибке. 

Чтобы перестроить индекс, необходимо удалить его (если он существует), заново создать индекс в службе и перезагрузить, получив данные из основного хранилища данных. Кроме того, для восстановления индексов можно обратиться в [службу поддержки клиентов](), если произошел региональный сбой.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Масштабирование вверх или вниз
Каждая поисковая служба стартует минимум с одной реплики и одного раздела. Если вы подписались на выделенные ресурсы с помощью [ценовой категории "Базовый" или "Стандартный"](search-limits-quotas-capacity.md), то вы можете щелкнуть элемент **МАСШТАБ** на панели мониторинга службы, чтобы скорректировать количество секций и реплик, используемых службой.

При добавлении емкости или мощности с помощью какого-либо ресурса служба начинает использовать его автоматически. Никаких дополнительных действий с вашей стороны не требуется, но будет небольшая задержка, прежде чем произойдет задействование нового ресурса. Подготовка дополнительных ресурсов может занять 15 и более минут.

 ![][10]

### <a name="add-replicas"></a>Добавление реплик
Увеличение количества запросов в секунду (QPS) или достижение высокой производительности осуществляется посредством добавления реплик. Каждая реплика имеет одну копию индекса, так что добавление еще ​​одной реплики означает перенос еще одного индекса, доступного для обработки запросов службы. Для обеспечения высокой доступности требуется не менее 3 реплик (дополнительные сведения см. в статье [Масштабирование уровней ресурсов для рабочих нагрузок запросов и индексирования в Поиске Azure](search-capacity-planning.md)).

Служба поиска с большим количеством реплик может загружать баланс поисковых запросов по большему числу индексов. Учитывая уровень объема запросов, пропускная способность запросов будет выше, когда существует больше копий индекса, доступных для обслуживания запроса. При возникновении задержки запросов возможен позитивный эффект на производительность, как только дополнительные реплики окажутся в сети.

Несмотря на то, что дополнительные реплики повышают пропускную способность, она в точности не удваивается или утраивается при добавлении реплик к вашей службе. Все приложения поиска могут зависеть от внешних факторов, которые могут противоречить показателям производительности запросов. Сложные запросы и задержки в сети являются факторами, влияющими на изменение времени ответа на запрос.

### <a name="add-partitions"></a>Добавление разделов
Большинство приложений-служб имеет встроенную потребность в большем количестве реплик, а не секций. В случае необходимости повысить количество документов нужно добавить секции, если вы подписаны на службу ценовой категории "Стандартный". Для категории "Базовый" дополнительные секции не предоставляются.

Для категории "Стандартный" секции добавляются кратно 12 (в частности, 1, 2, 3, 4, 6 или 12). Это артефакт сегментирования. Индекс создается в 12 сегментах, которые все вместе могут храниться в одной секции или равномерно распределяться по 2, 3, 4, 6 или 12 секциям (один сегмент на секцию).

### <a name="remove-replicas"></a>Удаление реплик
После окончания периода большого количества запросов вы, скорее всего, захотите сократить число реплик, когда нагрузки поисковых запросов нормализуются (например, после окончания периода сезонных распродаж).

Чтобы это сделать, переместите бегунок реплики обратно к меньшему значению. Больше с вашей стороны ничего делать не нужно. Уменьшение количества реплик освобождает виртуальные машины в центре обработки данных. Теперь ваши запросы и операции приема данных будут работать на меньшем количестве виртуальных машин, чем раньше. Минимальный предел - одна реплика.

### <a name="remove-partitions"></a>Удаление разделов
В отличие от удаления реплик, не требующего дополнительных усилий с вашей стороны, удаление пространства хранения - более сложная задача, особенно если вы используете больше хранилищ, чем может быть удалено. Например, если ваше решение использует три секции, то уменьшение размера до одной или двух секций приведет к ошибке, так как новое дисковое пространство меньше, чем требуется. Поэтому, как вы могли предположить, в такой ситуации рекомендуется удалить индексы или документы в соответствующем индексе для освобождения пространства, или сохранить текущую конфигурацию.

Не существует метода, задающего конкретные сегменты индекса, хранящиеся в каждом разделе. Каждый раздел обеспечивает примерно 25 ГБ в хранилище, так что вам нужно будет сократить хранилище до размера, который может быть размещен на имеющемся количестве разделов. Если вы хотите вернуться к одному разделу, то нужно вместить в него все 12 сегментов.

Чтобы помочь при планировании работы, вы можете проверить хранилище (с использованием раздела [Получение статистики индексов](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)), чтобы посмотреть, какой объем на самом деле используется. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Рекомендации по масштабированию и развертыванию
В этом 30-минутном видеоролике представлены рекомендации для расширенных сценариев развертывания, включая геораспределение рабочих нагрузок. См. также статью [Рекомендации по производительности и оптимизации Поиска Azure](search-performance-optimization.md), в которой рассматриваются те же вопросы.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Дальнейшие действия
После ознакомления с операциями, выполняемыми в рамках администрирования службы, рассмотрите различные подходы к управлению службами:

* [PowerShell](search-manage-powershell.md)

Ознакомьтесь также со статьей [Рекомендации по производительности и оптимизации Поиска Azure](search-performance-optimization.md) (если вы ее еще не прочитали) и при необходимости просмотрите видеоролик, упомянутый в предыдущем разделе. Это позволит вам лучше понять и освоить рекомендуемые методы.

<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png




