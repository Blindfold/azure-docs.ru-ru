---
title: "Репликация многоуровневого веб-приложения на основе IIS с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье описывается репликация виртуальных машин веб-фермы IIS с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b23624fc7e82af1cb593a1aedd138ae0d6637ae7
ms.lasthandoff: 03/29/2017


---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Репликация многоуровневого веб-приложения на основе IIS с помощью Azure Site Recovery

## <a name="overview"></a>Обзор


Программные средства являются механизмом обеспечения эффективности бизнес-операций в организации. Разные веб-приложения используются для различных целей. Важнейшими для деятельности организации являются приложения обработки платежных ведомостей, финансовые приложения и сайты для обслуживания клиентов. Постоянное поддержание работы этих приложений позволяет избежать потери производительности и, что более важно, — сохранить репутацию организации.

Важнейшие веб-приложения обычно настраиваются как многоуровневые приложения с поддержкой Интернета, базы данных и приложений на различных уровнях. Кроме того, что они распределяются по различным уровням, эти приложения могут также использовать несколько серверов на каждом уровне для балансировки трафика. Более того, сопоставления между различными уровнями и на веб-сервере могут основываться на статических IP-адресах. При отработке отказа некоторые из этих сопоставлений потребуется обновить, особенно если на веб-сервере настроено несколько веб-сайтов. В случае веб-приложений, использующих SSL, необходимо будет обновить привязки сертификатов.

Традиционные методы восстановления, не основанные на репликации, включают в себя архивацию различных файлов конфигурации, параметров реестра, привязок, настраиваемых компонентов (COM или .NET), содержимого и сертификатов, а также восстановление файлов с помощью действий, выполняемых вручную. Эти методы весьма трудоемки, к тому же они могут приводить к ошибкам и не являются масштабируемыми. Вполне вероятно, что вы можете забыть об архивации сертификатов и у вас не останется другого выбора, кроме как приобрести новые сертификаты для сервера после отработки отказа.

Хорошее решение по аварийному восстановлению должно позволять моделировать планы восстановления для сложной архитектуры приложений, а также добавлять настраиваемые действия для обработки сопоставлений приложения между различными уровнями. Таким образом, в случае аварии точное решение в один щелчок приведет к меньшему целевому времени восстановления (RTO).


В этой статье описывается защита веб-приложения на основе IIS с помощью [Azure Site Recovery](site-recovery-overview.md). Здесь мы рассмотрим передовые методы для репликации трехуровневого веб-приложения на основе IIS в Azure, выполнение отработки аварийного восстановления и выполнение отработки отказа приложения в Azure. 

 
## <a name="prerequisites"></a>Предварительные требования

Прежде чем продолжить, ознакомьтесь со следующими статьями:

1. [Репликация виртуальных машин VMware в Azure с помощью Site Recovery](site-recovery-vmware-to-azure.md)
1. [Проектирование сети для аварийного восстановления](site-recovery-network-design.md)
1. [Тестовая отработка отказа в Azure в Site Recovery](./site-recovery-test-failover-to-azure.md)
1. [Отработка отказа в Site Recovery](site-recovery-failover.md)
1. [Защита Active Directory и DNS с Azure Site Recovery](site-recovery-active-directory.md)
1. [Защита SQL Server с помощью аварийного восстановления SQL Server и Azure Site Recovery](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Модели развертывания
Веб-приложение на основе IIS обычно следует одной из моделей развертывания, представленных ниже.

**Модель развертывания 1.** Веб-ферма на основе IIS с маршрутизацией запросов приложений (ARR), сервером IIS и Microsoft SQL Server. 

![Модель развертывания](./media/site-recovery-iis/deployment-pattern1.png)

**Модель развертывания 2.** Веб-ферма на основе IIS с маршрутизацией запросов приложений (ARR), сервером IIS, сервером приложений и Microsoft SQL Server. 


![Модель развертывания](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Поддержка Site Recovery

Чтобы написать эту статью, использовались виртуальные машины VMware с сервером IIS версии 7.5 под управлением Windows Server 2012 R2 Enterprise. Так как репликация Site Recovery не зависит от приложения, описанные здесь рекомендации подходят для сценариев, представленных ниже, а также различных версий сервера IIS.

### <a name="source-and-target"></a>Исходный и целевой объект

**Сценарий** | **На дополнительный сайт** | **В Azure**
--- | --- | ---
**Hyper-V** | Да | Да
**VMware** | Да | Да
**Физический сервер** | Нет | Да

## <a name="replicate-virtual-machines"></a>Репликация виртуальных машин

Используйте [это руководство](site-recovery-vmware-to-azure.md), чтобы реплицировать все виртуальные машины веб-фермы IIS в Azure. 

Если вы используете статический IP-адрес — укажите нужный IP-адрес для виртуальной машины в разделе "Вычисления и сеть" для параметра [**Целевой IP-адрес**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties). 

![Целевой IP-адрес](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Создание плана восстановления

План восстановления в многоуровневом приложении позволяет выполнять виртуализацию отработки отказов различных уровней, а значит — поддерживает целостность на уровне приложения. При создании плана восстановления для многоуровневого веб-приложения следует выполнить действия, приведенные ниже.  Дополнительные сведения см. в статье [Создание планов восстановления](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Добавление виртуальных машин в группы отработки отказа
Обычное многоуровневое веб-приложение IIS содержит уровень базы данных с виртуальными машинами SQL, веб-уровень, включающий сервер IIS, а также уровень приложения. Добавьте все эти виртуальные машины в разные группы в соответствии с уровнем, как описано ниже. Дополнительные сведения см. в разделе [Настройка плана восстановления](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Создайте план восстановления. Добавьте виртуальные машины уровня базы данных в группу 1, чтобы они были выключенными последними, а начинали работу — первыми. 

1. Добавьте виртуальные машины уровня приложения в группу 2, чтобы они начинали работу сразу после включения уровня базы данных.

1. Добавьте виртуальные машины веб-уровня в группу 3, чтобы они начинали работу сразу после включения уровня приложения.

1. Добавьте виртуальные машины с балансировкой нагрузки в группу 4, чтобы они начинали работу сразу после включения веб-уровня.


### <a name="adding-scripts-to-the-recovery-plan"></a>Добавление скриптов в план восстановления
Вам может потребоваться выполнить некоторые операции после отработки отказа виртуальных машин Azure или тестовой отработки отказа, чтобы настроить правильную работу веб-фермы IIS. Вы можете настроить автоматическое выполнение таких операций после отработки отказа, как обновление записи DNS, изменение привязки сайта, изменение строки подключения, добавив соответствующие скрипты в план восстановления, как описано ниже. Дополнительные сведения см. в разделе [Добавление скриптов](./site-recovery-create-recovery-plans.md#add-scripts).

#### <a name="dns-update"></a>Обновление DNS
Если в DNS настроено динамическое обновление, тогда виртуальные машины обычно обновляют IP-адрес сразу после запуска. Если вы хотите добавить явное действие для обновления DNS (обновление IP-адреса виртуальных машин), тогда добавьте этот [скрипт для обновления IP-адреса в DNS](https://aka.ms/asr-dns-update) как завершающее действие в группах плана восстановления.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Строка подключения в файле web.config приложения
Строка подключения определяет базу данных, с которой обменивается данными веб-сайт. 

Если строка подключения содержит имя виртуальной машины базы данных, никаких дополнительных действий после отработки отказа не потребуется и приложение сможет автоматически обмениваться данными с базой данных. Кроме того, если IP-адрес виртуальной машины базы данных сохраняется, нет необходимости обновлять строку подключения. Если строка подключения ссылается на виртуальную машину базы данных с помощью IP-адреса, ее необходимо обновить после отработки отказа. Например:  строка подключения, приведенная ниже, указывает на базу данных с IP-адресом 127.0.1.2. 

        <?xml version="1.0" encoding="utf-8"?> 
        <configuration> 
        <connectionStrings> 
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" /> 
        </connectionStrings> 
        </configuration>

Вы можете обновить строку подключения на веб-уровне, добавив [скрипт обновления для подключения IIS](https://aka.ms/asr-update-webtier-script-classic) в группу 3 в плане восстановления.

#### <a name="site-bindings-for-the-application"></a>Привязки сайта для приложения
Каждый сайт содержит сведения о привязке, которые включают тип привязки, IP-адрес, на котором сервер IIS прослушивает запросы к сайту, номер порта и имена узлов сайта. Во время отработки отказа эти привязки необходимо обновить только в том случае, если изменяется связанный с ними IP-адрес. 

> [!NOTE]
> 
> Если для привязки сайта вы выбрали параметр "Все неназначенные", как показано в примере ниже, обновление этой привязки после отработки отказа не требуется. Не нужно обновлять привязку к сайту в случае, если после отработки отказа IP-адрес, связанный с сайтом, остается прежним. (Сохранение IP-адреса зависит от архитектуры сети и подсетей, назначенных основному сайту и сайту восстановления, что для организации может быть нецелесообразно или наоборот.) 

![Привязка SSL](./media/site-recovery-iis/sslbinding.png)

Если с сайтом связан IP-адрес, вам необходимо будет обновить все привязки сайта новым IP-адресом. Чтобы изменить привязки сайта, вы можете добавить [скрипт обновления веб-уровня IIS](https://aka.ms/asr-web-tier-update-runbook-classic) в группу 3 в плане восстановления. 


#### <a name="update-load-balancer-ip-address"></a>Обновление IP-адреса балансировщика нагрузки
Если у вас есть виртуальная машина с маршрутизацией запросов приложений, [добавьте скрипт для отработки отказа при использовании маршрутизации запросов приложений IIS](https://aka.ms/asr-iis-arrtier-failover-script-classic) в группу 4, чтобы обновить IP-адрес.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Привязка SSL-сертификата для подключения HTTPS
С веб-сайтами может быть связан SSL-сертификат, который обеспечивает безопасный обмен данными между веб-сервером и браузером пользователя. Если у веб-сайта есть HTTPS-подключение и связанная HTTPS-привязка сайта к IP-адресу сервера IIS с привязкой SSL-сертификата, необходимо добавить новую привязку сайта для сертификата с IP-адресом виртуальной машины IIS после отработки отказа. 

SSL-сертификат можно выдать с использованием: 

а) полного доменного имени веб-сайта;<br>
б) сервера;<br>
в) группового сертификата для доменного имени;<br>
г) IP-адреса. Если SSL-сертификат сформирован для IP-адреса сервера IIS, другой SSL-сертификат нужно создать для IP-адреса сервера IIS на сайте Azure и должна быть создана дополнительная привязка SSL для этого сертификата. Таким образом, рекомендуется не использовать SSL-сертификат для IP-адреса. Этот вариант используется не часто, а вскоре он будет объявлен устаревшим в соответствии с изменениями CA/Browser Forum.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Обновление зависимости между веб-уровнем и уровнем приложения
Если у приложения есть зависимость на основе IP-адреса виртуальных машин, вам необходимо обновить эту зависимость после отработки отказа.

## <a name="doing-a-test-failover"></a>Тестовая отработка отказа
Чтобы выполнить тестовую отработку отказа, используйте [это руководство](site-recovery-test-failover-to-azure.md).

1.    Перейдите на портал Azure и выберите хранилище служб восстановления.
1.    Щелкните план восстановления, созданный для веб-фермы IIS.
1.    Щелкните "Тестовая отработка отказа".
1.    Выберите точку восстановления и виртуальную сеть Azure, чтобы запустить тестовую отработку отказа.
1.    Как только будет запущена вторичная среда, вы сможете выполнить проверку.
1.    После завершения проверки вы можете выбрать Validations complete (Проверка завершена), и среда тестовой отработки отказа будет удалена.

## <a name="doing-a-failover"></a>Отработка отказа
При выполнении отработки отказа используйте [это руководство](site-recovery-failover.md).

1.    Перейдите на портал Azure и выберите хранилище служб восстановления.
1.    Щелкните план восстановления, созданный для веб-фермы IIS.
1.    Щелкните "Отработка отказа".
1.    Выберите точку восстановления, чтобы запустить отработку отказа.

## <a name="next-steps"></a>Дальнейшие действия
Вы можете узнать дополнительные сведения о [репликации других приложений](site-recovery-workload.md) с помощью Site Recovery. 

