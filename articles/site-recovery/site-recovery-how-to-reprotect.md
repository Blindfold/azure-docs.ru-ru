---
title: "Как повторно включить защиту виртуальных машин, восстанавливаемых из Azure на локальный сайт | Документация Майкрософт"
description: "После отработки отказа виртуальных машин в Azure можно инициировать восстановление размещения, чтобы вернуть их в локальную среду. Узнайте, как повторно включить защиту перед восстановление размещения."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3156ca5b2b8ba836e94d79a97b28bf591c799b48
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Повторное включение защиты виртуальных машин, восстанавливаемых из Azure на локальный сайт

## <a name="overview"></a>Обзор
В этой статье описывается, как повторно включить защиту виртуальных машин Azure на локальном сайте с переносом из Azure. Чтобы восстановить размещение виртуальных машин VMware или физических серверов Windows или Linux, для которых была выполнена отработка отказа с локального сайта в Azure с использованием [репликации виртуальных машин VMware и физических серверов в Azure с помощью службы Azure Site Recovery](site-recovery-failover.md), следуйте инструкциям, изложенным в этой статье.

> [!WARNING]
> Если вы [выполнили миграцию](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), переместили виртуальную машину в другую группу ресурсов или удалили виртуальную машину Azure, то после этого невозможно будет выполнить восстановление размещения.


После того как защита будет повторно включена и начнется репликация защищенных виртуальных машин, вы сможете запустить восстановление размещения виртуальных машин, чтобы вернуть их на локальный сайт.

Комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Просмотрите видео ниже, чтобы получить общее представление о том, как выполнить отработку отказа из Azure на локальный сайт.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Предварительные требования
Ниже приведено несколько предварительных действий, которые необходимо выполнить или рассмотреть при подготовке к повторному включению защиты.

* Если управление виртуальными машинами, размещение которых необходимо восстановить, осуществляется с помощью сервера vCenter Server, необходимо убедиться в наличии требуемых разрешений для обнаружения виртуальных машин на серверах vCenter Server. [Подробная информация](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).

> [!WARNING] 
> Если на локальном главном целевом сервере или виртуальной машине есть моментальные снимки, повторное включение защиты завершится сбоем. Их можно удалить с главного целевого сервера, прежде чем продолжить повторное включение защиты. Моментальные снимки на виртуальной машине будут автоматически объединены во время выполнения задания повторного включения защиты.

* Прежде чем выполнить восстановление размещения, потребуется создать два дополнительных компонента.
  * **Создайте сервер обработки**. Сервер обработки получает данные из защищенных виртуальных машин в Azure и отправляет их на локальный сайт. Между сервером обработки и защищенной виртуальной машиной должна быть настроена сеть с низкой задержкой. Таким образом, при использовании подключения ExpressRoute сервер обработки может находиться в локальной среде, а при использовании VPN-подключения — в Azure.
  * **Создайте главный целевой сервер**. Этот сервер получает данные восстановления размещения. На сервере управления, созданном локально, главный целевой сервер установлен по умолчанию. Но в зависимости от объема трафика восстановления размещения для него может потребоваться создать отдельный главный целевой сервер.
        * [Для виртуальной машины Linux необходим главный целевой сервер Linux.](site-recovery-how-to-install-linux-master-target.md)
        * Для виртуальной машины Windows необходим главный целевой сервер Windows. Вы можете повторно использовать локальный сервер обработки и главные целевые машины.
* При восстановлении размещения локальная среда должна содержать сервер конфигурации. Во время восстановления размещения виртуальная машина должна существовать в базе данных сервера конфигурации. В противном случае восстановление размещения завершится сбоем. Обеспечьте регулярное плановое резервное копирование сервера. В случае аварии потребуется восстановить сервер с тем же IP-адресом, чтобы обеспечить работу восстановления размещения.
* В конфигурации главной целевой виртуальной машины в VMware установите для параметра disk.EnableUUID значение true. Если этого параметра нет, добавьте его. Это позволит предоставить согласованный UUID для правильного подключения диска виртуальной машины (VMDK).
* *Вы не можете использовать технологию Storage vMotion на главном целевом сервере*. Из-за этого восстановление размещения может завершиться сбоем. Виртуальная машина не запустится, так как для нее не будет доступных дисков. Чтобы избежать этого, исключите главные целевые серверы из списка vMotion.
* Поэтому необходимо добавить новый диск на главный целевой сервер. Этот диск называется диском хранения. Добавьте новый диск и отформатируйте его.
* Другие предварительные требования для главного целевого сервера см. в разделе [Что нужно проверить после установки главного целевого сервера](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Зачем нужно VPN-подключение типа "сеть — сеть" или подключение ExpressRoute для репликации данных на локальный сайт?
Если репликация из локальной среды в Azure может осуществляться через Интернет или подключение ExpressRoute с общедоступным пирингом, при повторном включении защиты и восстановлении размещения для репликации данных требуется VPN-подключение типа "сеть — сеть". Вам понадобится сеть, через которую виртуальные машины в Azure, перенесенные при отработке отказа, смогут связываться (проверять связь) с локальным сервером конфигурации. Вы можете также развернуть сервер обработки в этой сети Azure виртуальной машины, для которой выполнена отработка отказа. Кроме того, этот сервер обработки должен иметь возможность взаимодействовать с локальным сервером конфигурации.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Когда следует установить сервер обработки в Azure?


Виртуальные машины Azure, для которых нужно повторно включить защиту, отправляют данные репликации на сервер обработки. Сеть должна быть настроена таким образом, чтобы виртуальные машины Azure могли связаться с сервером обработки.

Сервер обработки можно развернуть в Azure или задействовать существующий сервер обработки, который использовался во время отработки отказа. Важно учитывать задержку при отправке данных из виртуальных машин Azure на сервер обработки.

Если настроено подключение ExpressRoute, то для отправки данных можно использовать локальный сервер обработки, так как между ним и виртуальной машиной низкая задержка.

 ![Схема архитектуры для ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Однако если вы настроили только VPN-подключение типа "сеть — сеть", то мы рекомендуем развернуть сервер обработки в Azure.

 ![Схема архитектуры для VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Помните, что репликация будет осуществляться только через VPN-подключение типа "сеть — сеть" или частный пиринг сети ExpressRoute. Убедитесь, что этот сетевой канал обеспечивает достаточную пропускную способность.

Дополнительные сведения об установке сервера обработки в Azure см. в статье [Управление сервером обработки, запущенным в Azure (модель Resource Manager)](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Мы всегда рекомендуем использовать при восстановлении размещения сервер обработки на базе Azure. Производительность репликации повышается, если сервер обработки расположен ближе к реплицируемой виртуальной машине (в Azure — к компьютеру, для которого выполняется отработка отказа). Однако при подтверждении концепции или демонстрации можно использовать локальный сервер обработки вместе с ExpressRoute и частным пирингом, чтобы ускорить работу.



### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Какие порты должны быть открыты на разных компонентах для корректной работы повторной защиты?

![Отработка отказа и восстановление размещения на всех портах](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>Какой главный целевой сервер необходимо использовать для повторного включения защиты?
Главный целевой сервер, расположенный в локальной среде, нужен для получения данных от сервера обработки и их записи в VMDK-файл локальной виртуальной машины. Если вы защищаете виртуальные машины Windows, то нужен главный целевой сервер Windows. Вы можете повторно использовать локальный сервер обработки и главный целевой сервер. <!-- !todo component --> Для виртуальных машин Linux вам потребуется настроить дополнительный главный целевой сервер Linux, развернутый в локальной среде.


Чтобы ознакомиться с инструкциями по установке главного целевого сервера, перейдите по следующим ссылкам:

* [Выполнение единой установки Site Recovery](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Как установить главный целевой сервер Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Что нужно проверить после установки главного целевого сервера

* Если виртуальная машина размещена локально на сервере vCenter Server, то главному целевому серверу требуется доступ к VMDK-файлу этой локальной виртуальной машины. Это необходимо для записи реплицированных данных на диски виртуальной машины. Убедитесь, что хранилище данных локальной виртуальной машины подключено к узлу главного целевого сервера с доступом на чтение и запись.

* Если виртуальная машина не размещена локально на сервере vCenter Server, то во время повторного включения защиты службе Site Recovery потребуется создать виртуальную машину. Она будет создана на узле ESX, на котором создан главный целевой сервер. Выбирайте узел ESX внимательно, чтобы виртуальная машина для восстановления размещения была создана на нужном узле.

* *Вы не можете использовать технологию Storage vMotion на главном целевом сервере*. Из-за этого восстановление размещения может завершиться сбоем. Виртуальная машина не запустится, так как для нее не будет доступных дисков.

> [!WARNING]
> Если на главном целевом сервере повторно включается защита после Storage vMotion, диски защищенных виртуальных машин, подключенные к главному целевому серверу, будут перенесены на целевой сервер vMotion. Если после этого попытаться восстановить размещение, отключение диска завершается неудачей и отображается сообщение о том, что диски не найдены. После этого найти диски в учетных записях хранения становится очень трудно. Вам потребуется найти их вручную и подключить к виртуальной машине. После этого можно загрузить локальную виртуальную машину.

* Поэтому на имеющийся главный целевой сервер Windows необходимо добавить новый диск. Этот диск называется диском хранения. Добавьте новый диск и отформатируйте его. Диск хранения используется для остановки точек во времени при репликации виртуальной машины обратно на локальный сайт. Ниже перечислены некоторые критерии диска хранения, без которых он не будет отображаться для главного целевого сервера.

   * Том не должен использоваться ни для какой иной цели, например как цель репликации и т. д.

   * Том не должен находиться в режиме блокировки.

   * Том не должен быть томом кэша. На томе не должен присутствовать установленный экземпляр главного целевого сервера. Пользовательский том установки сервера обработки и главного целевого сервера не может использоваться как том хранения. Если на этом томе установлен сервер обработки и главный целевой сервер, он используется в качестве тома кэша главного целевого сервера.

   * Том не поддерживает тип файловой системы FAT или FAT32.

   * Емкость тома не должна быть нулевой.

   * Том хранения по умолчанию для Windows — это том R.

   * Том хранения по умолчанию для Linux — /mnt/retention.
   
   > [!IMPORTANT]
   > Если вы используете существующий компьютер с сервером конфигурации и сервером обработки или компьютер с сервером обработки и главным целевым сервером, нужно добавить новый диск. Этот диск должен удовлетворять перечисленным выше требованиям. Если диск хранения отсутствует, в раскрывающемся списке на портале ничего не отображается. После добавления диска на локальный главный целевой сервер для его появления на портале может потребоваться до 15 минут. Если через 15 минут диск так и не появился, вы также можете обновить конфигурацию сервера.



* Для виртуальной машины Linux, для которой выполнена отработка отказа, необходим главный целевой сервер Linux, а для виртуальной машины Windows (также после отработки отказа) — главный целевой сервер Windows.

* Установите инструменты VMware на главном целевом сервере. Без них не удастся обнаружить хранилища данных на узле ESXi главного целевого сервера.

* Включите параметр disk.EnableUUID=true на главной целевой виртуальной машине с использованием свойств vCenter. <!-- !todo Needs link. -->

* К главному целевому серверу должно быть подключено по крайней мере одно хранилище данных файловой системы виртуальной машины (VMFS). Если такового нет, сведения о **хранилище данных** на странице повторного включения защиты отображаться не будут, и вы не сможете продолжить работу.

* На дисках главного целевого сервера не должно быть моментальных снимков. В противном случае при повторном включении защиты или восстановлении размещения произойдет сбой.

* На главном целевом сервере не может находиться контроллер Paravirtual SCSI. На нем может быть только контроллер LSI Logic. Если контроллер LSI Logic отсутствует, при повторном включении защиты произойдет сбой.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Инструкции по повторному включению защиты

Перед повторным включением защиты убедитесь, что вы установили [сервер обработки](site-recovery-vmware-setup-azure-ps-resource-manager.md) в Azure, а также [главный целевой сервер Linux](site-recovery-how-to-install-linux-master-target.md) или Windows в локальной среде.

> [!NOTE]
> После загрузки виртуальной машины в Azure потребуется некоторое время для повторной регистрации агента на сервере конфигурации (не более 15 минут). Если включить повторную защиту в течение этого времени, произойдет сбой и на экране отобразится сообщение о том, что агент не установлен. Подождите несколько минут, а затем включите повторную защиту еще раз.



1. В разделе **Хранилище** > **Реплицированные элементы** щелкните виртуальную машину, для которой проводилась отработка отказа, правой кнопкой мыши и выберите **Защитить повторно**. Вы также можете щелкнуть машину и выбрать параметр **Защитить повторно**, используя кнопки управления.
2. Обратите внимание, что в открывшейся колонке уже выбрано направление защиты **Из Azure на локальный компьютер**.
3. В полях **Главный целевой сервер** и **Сервер обработки** выберите локальный главный целевой сервер и сервер обработки.
4. Выберите **хранилище данных**, для которого нужно восстановить диски локальной среды. Используйте этот параметр, если локальная виртуальная машина была удалена и вам нужно создать диски. Хотя этот параметр не учитывается, если диски уже существуют, его значение необходимо указать.
5. Выберите диск хранения.
6. Политика восстановления размещения будет выбрана автоматически.
7. После нажатия кнопки **ОК** для повторного включения защиты запускается задание репликации виртуальной машины из Azure на локальный сайт. Ход выполнения операции можно отслеживать на вкладке **Задания** .

Если необходимо выполнить восстановление в альтернативное расположение (если локальная виртуальная машина удалена), выберите диск хранения и хранилище данных, настроенные для главного целевого сервера. При восстановлении расположения на локальный сайт виртуальные машины VMware в плане защиты восстановления размещения будут использовать то же хранилище данных, что и главный целевой сервер. В vCenter будет создана виртуальная машина.
Если вы хотите восстановить виртуальную машину Azure в имеющуюся локальную виртуальную машину, то ее локальные хранилища данных должны быть подключены с доступом на чтение и запись к узлу ESXi главного целевого сервера.
    ![Диалоговое окно "Защитить повторно"](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Защиту можно также включить повторно на уровне плана восстановления. Группу репликации можно повторно защитить только с помощью плана восстановления. В этом случае необходимо предоставить значения для каждой защищенной машины.

> [!NOTE]
> Защита группы репликации должна быть восстановлена с использованием того же главного целевого сервера. При выборе другого главного целевого сервера он не сможет предоставить общую временную точку.

> [!NOTE]
> Во время повторного включения защиты локальная виртуальная машина будет отключена. Это позволяет обеспечить согласованность данных во время репликации. Не включайте виртуальную машину после завершения повторного включения защиты.

После успешного повторного включения защиты виртуальная машина перейдет в защищенное состояние.

## <a name="next-steps"></a>Дальнейшие действия

После того как виртуальная машина перейдет в защищенное состояние, можно инициировать восстановление размещения. При этом будет завершена работа виртуальной машины в Azure и загружена локальная виртуальная машина. Поэтому в работе приложения будет незначительный простой. В этом случае выберите для восстановления размещения период, когда простой приложения допустим.

Дополнительные сведения см. в разделе [Инструкции по восстановлению размещения](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back).

## <a name="common-issues"></a>Распространенные проблемы

* Если виртуальные машины созданы при помощи шаблона, убедитесь, что диски каждой виртуальной машины имеют UUID. При возникновении конфликта UUID локальной виртуальной машины и главного целевого сервера, так как оба компонента созданы на основе одного шаблона, произойдет сбой повторного включения защиты. В таком случае нужно развернуть другой главный целевой сервер, созданный на основе другого шаблона.
* Если выполнить обнаружение vCenter в режиме пользователя только для чтения и защитить виртуальные машины, операция настройки защиты завершится успешно и отработка отказа будет работать. Во время повторного включения защиты происходит сбой отработки отказа, так как невозможно обнаружить хранилища данных. Признаком этой ситуации является отсутствие списка хранилищ данных при повторном включении защиты. Чтобы устранить эту проблему, вы можете обновить учетные данные vCenter, указав учетную запись с соответствующими разрешениями, и повторить задание. Дополнительные сведения см. в разделе [Репликация виртуальных машин VMware и физических серверов в Azure с помощью службы Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* При восстановлении размещения виртуальной машины Linux и ее последующем запуске в локальной среде вы видите, что с этой виртуальной машины удален пакет диспетчера сети. Это вызвано тем, что при восстановлении виртуальной машины в Azure пакет диспетчера сети удаляется.
* Если отработка отказа виртуальной машины Linux со статическим IP-адресом выполняется в Azure, IP-адрес предоставляется из DHCP. При восстановлении размещения в локальной среде виртуальная машина также будет получать IP-адрес из DHCP. Вручную войдите на виртуальную машину и снова настройте статический IP-адрес, если это необходимо. Виртуальная машина Windows может получать статический IP-адрес повторно.
* Если вы используете бесплатный выпуск ESXi версии 5.5 или vSphere Hypervisor версии 6, отработка отказа будет выполнена успешно, но восстановление размещения завершится сбоем. Чтобы включить восстановления размещения, необходимо выполнить обновление до любой из версий с ознакомительной лицензией.
* Если сервер конфигурации недоступен с сервера обработки, проверьте возможность подключения к серверу конфигурации по протоколу Telnet через порт 443. Вы также можете проверить связь с сервером конфигурации из сервера обработки. Сервер обработки должен также отвечать на пульс, когда он подключен к серверу конфигурации.
* Если вы пытаетесь восстановить размещения на альтернативный сервер vCenter, убедитесь, что новый сервер vCenter обнаружен, как и главный целевой сервер. Характерный симптом: хранилища недоступны и отсутствуют в диалоговом окне **Повторная защита**.
* Сервер Windows Server 2008 R2 с пакетом обновления 1 (SP1), защищенный как физический локальный сервер, не может быть восстановлен из Azure на локальный сайт.

