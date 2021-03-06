---
title: "Таблица поддержки Azure Site Recovery для репликации в Azure | Документация Майкрософт"
description: "В этой статье перечислены операционные системы и компоненты, поддерживаемые Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 711fb0715b7f12e12a742136f75af8069cbc83d8
ms.lasthandoff: 04/17/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Таблица поддержки Azure Site Recovery для репликации в Azure

> [!div class="op_single_selector"]
> * [Репликация в Azure](site-recovery-support-matrix-to-azure.md)
> * [Репликация на принадлежащий клиенту вторичный сайт](site-recovery-support-matrix-to-sec-site.md)


В этой статье кратко перечислены поддерживаемые конфигурации и компоненты Azure Site Recovery для репликации и восстановления в Azure. Дополнительные сведения о необходимых компонентах Azure Site Recovery см. [здесь](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Поддержка вариантов развертывания

**Развертывание** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)** |
--- | --- | ---
**Портал Azure** | Развертывание локальных виртуальных машин VMware в службу хранилища Azure с использованием хранилища и сетей на основе модели Resource Manager или классической модели.<br/><br/> Доступна отработка отказа на виртуальные машины на основе модели Resource Manager или классической модели. | Из локальных виртуальных машин Hyper-V в хранилище Azure, используя хранилище и сети модели Resource Manager или классической модели.<br/><br/> Доступна отработка отказа на виртуальные машины на основе модели Resource Manager или классической модели.
**Классический портал.** | Доступен только режим обслуживания. Создать хранилища невозможно. | Доступен только режим обслуживания.
**PowerShell** | Сейчас не поддерживается. | Поддерживаются


## <a name="support-for-datacenter-management-servers"></a>Поддержка серверов управления центра обработки данных

### <a name="virtualization-management-entities"></a>Объекты управления виртуализацией

**Развертывание** | **Поддержка**
--- | ---
**Виртуальная машина VMware или физический сервер** | vSphere 6.0, 5.5 или 5.1 с последними обновлениями
**Hyper-V (с Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 и System Center Virtual Machine Manager 2012 R2.

  >[!Note]
  > В настоящее время облако System Center Virtual Machine Manager 2016, сочетающее узлы Windows Server 2016 и Windows Server 2012 R2, не поддерживается.

### <a name="host-servers"></a>Серверы узлов

**Развертывание** | **Поддержка**
--- | ---
**Виртуальная машина VMware или физический сервер** | vCenter 5.5 или vCenter 6.0 (поддержка только функций vCenter 5.5) 
**Hyper-V (с и без Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 с последними обновлениями.<br></br>Если используется SCVMM, узлы Windows Server 2016 должны управляться сервером SCVMM 2016.


  >[!Note]
  >В настоящее время сайт Hyper-V, сочетающий узлы под управлением Windows Server 2016 и 2012 R2, не поддерживается. В настоящее время не поддерживается восстановление виртуальных машин, размещенных на узле Windows Server 2016, в альтернативное расположение.

## <a name="support-for-replicated-machine-os-versions"></a>Поддержка версий ОС для реплицируемых виртуальных машин

При репликации в Azure защищенные виртуальные машины должны соответствовать [требованиям Azure](#failed-over-azure-vm-requirements).
В таблице ниже представлены сведения о поддержке реплицируемых операционных систем в различных сценариях развертывания при использовании Azure Site Recovery. Эта поддержка относится к любой рабочей нагрузке, выполняемой в указанной ОС.

 **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)** |
--- | --- |
64-разрядная версия Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 как минимум с пакетом обновления 1 (SP1).<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2. <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2. <br/><br/> Oracle Enterprise Linux 6.4, 6.5 с ядром, совместимым с Red Hat, или с ядром Unbreakable Enterprise Kernel Release 3 (UEK3). <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> SUSE Linux Enterprise Server 11 SP4 <br/>(Обновление реплицируемых компьютеров с SLES 11 SP3 до SLES 11 SP4 не поддерживается. Если реплицируемый компьютер обновлен с SLES 11 SP3 до SLES 11 SP4, вам потребуется отключить репликацию и включить повторную защиту компьютера после обновления.) | Любая гостевая ОС, [поддерживаемая Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!IMPORTANT]
>(Применимо к репликации серверов VMware и физических серверов в Azure.)
>
> На серверах Red Hat Enterprise Linux Server 7+ и CentOS 7+ поддерживается версия ядра 3.10.0-514, начиная с Azure Site Recovery Mobility Service версии 9.8.<br/><br/>
> Для клиентов, использующих ядро 3.10.0-514 с версией службы мобильности ниже версии 9.8, требуется отключить репликацию, обновить версию службы мобильности до 9.8, а затем повторно включить репликацию.  

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Поддерживаемые файловые системы и конфигурации гостевого хранилища в Linux (серверы VMware и физические серверы)

Следующие файловые системы и программа настройки хранилища поддерживаются на серверах Linux в среде VMware либо на физических серверах:
* файловые системы — ext3, ext4, ReiserFS (только Suse Linux Enterprise Server), XFS (только до версии 4);
* диспетчер томов — LVM2;
* многопутевые программы — Device Mapper.

Физические серверы с контроллером хранилища HP CCISS не поддерживаются.

>[!Note]
> На серверах Linux следующие каталоги (если они настроены в качестве отдельных разделов и файловых систем) должны находиться на одном диске (диск ОС) исходного сервера: /(root), /boot, /usr, /usr/local, /var, /etc.<br/><br/>
> В настоящее время функции XFS v5 (например, контрольная сумма метаданных) не поддерживаются службой ASR в файловых системах XFS. Убедитесь, что в файловых системах XFS не используются функции v5. Вы можете использовать служебную программу xfs_info, чтобы проверить системный блок XFS для раздела. Если для параметра ftype задано значение 1, будут использоваться функции XFSv5. 
>


## <a name="support-for-network-configuration"></a>Поддержка конфигурации сети
В таблицах ниже представлены сведения о поддержке конфигурации сети в различных сценариях развертывания с использованием Azure Site Recovery для репликации в Azure.

### <a name="host-network-configuration"></a>Конфигурация сети узла

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | ---
Объединение сетевых адаптеров | Да<br/><br/>Не поддерживается на физических компьютерах| Да
Виртуальная локальная сеть | Да | Да
IPv4 | Да | Да
IPv6 | Нет | Нет

### <a name="guest-vm-network-configuration"></a>Конфигурация сети гостевых виртуальных машин

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | ---
Объединение сетевых адаптеров | Нет | Нет
IPv4 | Да | Да
IPv6 | Нет | Нет
Статический IP-адрес (Windows) | Да | Да
Статический IP-адрес (Linux) | Нет | Нет
Несколько сетевых адаптеров | Да | Да

### <a name="failed-over-azure-vm-network-configuration"></a>Конфигурация сети виртуальных машин Azure после отработки отказа

**Сети Azure** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Да | Да
Внутренний балансировщик нагрузки | Да | Да
Внешний балансировщик нагрузки | Да | Да
Диспетчер трафика | Да | Да
Несколько сетевых адаптеров | Да | Да
Зарезервированный IP-адрес | Да | Да
IPv4 | Да | Да
Сохранение исходного IP-адреса | Да | Да


## <a name="support-for-storage"></a>Поддержка устройств и систем хранения
В таблицах ниже представлены сведения о поддержке конфигурации хранилища в различных сценариях развертывания с использованием Azure Site Recovery для репликации в Azure.

### <a name="host-storage-configuration"></a>Конфигурация хранилища узла

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Да для VMware<br/><br/> Нет для физических серверов | Недоступно
SMB 3.0 | Недоступно | Да
Сеть SAN (iSCSI) | Да | Да
Многопутевой (MPIO)<br></br>Протестировано с помощью Microsoft DSM, EMC PowerPath 5.7 SP4 и EMC PowerPath DSM для CLARiiON. | Да | Да

### <a name="guest-or-physical-server-storage-configuration"></a>Конфигурация хранилища гостя или физического сервера

**Конфигурация** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | ---
VMDK | Да | Недоступно
VHD (VHDX) | Недоступно | Да
Виртуальная машина 2-го поколения | Недоступно | Да
UEFI (EFI)| Нет | Да
Общий диск кластера | Да для VMware<br/><br/> Недоступно для физических серверов. | Нет
Зашифрованный диск | Нет | Нет
NFS | Нет | Недоступно
SMB 3.0 | Нет | Нет
RDM | Да<br/><br/> Недоступно для физических серверов. | Недоступно
Диск > 1 ТБ | Нет | Нет
Том с чередующимся диском > 1 ТБ<br/><br/> Управление логическими томами (LVM) | Да | Да
Дисковые пространства | Нет | Да
"Горячее" добавление или удаление диска | Нет | Нет
Исключение диска | Да | Да
Многопутевой (MPIO) | Недоступно | Да

**Служба хранилища Azure** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | ---
LRS | Да | Да
GRS | Да | Да
RA-GRS | Да | Да
"Холодное" хранилище | Нет | Нет
"Горячее" хранилище| Нет | Нет
Шифрование неактивных данных (SSE)| Да | Да
Хранилище уровня "Премиум" | Да | Да
Служба импорта и экспорта | Нет | Нет


## <a name="support-for-azure-compute-configuration"></a>Поддержка конфигурации службы вычислений Azure

**Компонент для вычислений** | **VMware или физический сервер** | **Hyper-V (с и без Virtual Machine Manager)**
--- | --- | --- | ---
Группы доступности | Да | Да
Концентратор | Да | Да  

## <a name="failed-over-azure-vm-requirements"></a>Требования к виртуальным машинам Azure после отработки отказа

Вы можете развернуть Site Recovery для репликации виртуальных машин и физических серверов под управлением любой операционной системы, поддерживаемой Azure. Поддерживаются большинство версий Windows и Linux. Для репликации в Azure локальные виртуальные машины должны соответствовать описанным ниже требованиям Azure.

**Сущность** | **Требования** | **Дополнительные сведения**
--- | --- | ---
**Операционная система на виртуальной машине** | В сценариях, включающих в себя репликацию из Hyper-V в Azure, служба Site Recovery защищает все операционные системы, [поддерживаемые Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Сведения о репликации виртуальных машин VMware и физических серверов см. в [предварительных требованиях](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) для Windows и Linux. | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Архитектура операционной системы на виртуальной машине** | 64-разрядная | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Размер диска операционной системы** | До 1023 ГБ. | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Число дисков операционной системы** | 1 | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Число дисков с данными** | Не менее 64 при репликации **виртуальных машин VMware в Azure** и не менее 16 при репликации **виртуальных машин Hyper-V в Azure**. | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Размер виртуального жесткого диска с данными** | До 1023 ГБ. | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Сетевые адаптеры** | Поддерживаются несколько адаптеров |
**Общий виртуальный жесткий диск** | Не поддерживается | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Диск FC** | Не поддерживается | Если не поддерживается, проверка на соответствие обязательным требованиям завершится с ошибкой.
**Формат жесткого диска** | VHD  <br/><br/> VHDX | Хотя Azure пока что не поддерживает формат VHDX, служба Site Recovery автоматически преобразует VHDX в VHD при отработке отказа с перемещением в Azure. При восстановлении к локальной системе виртуальные машины продолжают использовать формат VHDX.
**BitLocker** | Не поддерживается | Перед установкой защиты виртуальной машины, необходимо отключить шифрование BitLocker.
**Имя виртуальной машины** | От 1 до 63 символов, при этом допустимы только буквы, цифры и дефисы Имя виртуальной машины должно начинаться и заканчиваться буквой или цифрой. | Обновите значение в свойствах виртуальной машины в службе Site Recovery.
**Тип виртуальной машины** | Поколение 1<br/><br/> Поколение 2 — Windows | Поддерживаются виртуальные машины второго поколения с диском ОС типа "Базовый" (включая один или два тома данных в формате VHDX) и объемом менее 300 ГБ.<br></br>Виртуальные машины Linux второго поколения не поддерживаются. [Дополнительные сведения](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Поддержка действий хранилища служб восстановления

**Действие** | **VMware или физический сервер** | **Hyper-V (без Virtual Machine Manager)** | **Hyper-V (с Virtual Machine Manager)**
--- | --- | --- | ---
Перемещение хранилища между группами ресурсов<br/><br/> В пределах подписок и между ними | Нет | Нет | Нет
Перемещение хранилищ, сетей, виртуальных машин Azure между группами ресурсов<br/><br/> В пределах подписок и между ними | Нет | Нет | Нет


## <a name="support-for-provider-and-agent"></a>Поддержка поставщика и агента

**Имя** | **Описание** | **Последняя версия** | **Дополнительные сведения**
--- | --- | --- | --- | ---
**Поставщик Azure Site Recovery** | Координирует взаимодействие между локальными серверами и Azure <br/><br/> Устанавливается на локальные серверы Virtual Machine Manager или серверы Hyper-V, если сервер Virtual Machine Manager отсутствует. | 5.1.19 ([доступна на портале](http://aka.ms/downloaddra)) | [Новейшие функции и последние исправления](https://support.microsoft.com/kb/3155002)
**Унифицированная установка Azure Site Recovery (VMware в Azure)** | Координирует взаимодействие между локальными серверами VMware и Azure <br/><br/> Установлена на локальных серверах VMware | 9.3.4246.1 (доступна на портале) | [Новейшие функции и последние исправления](https://support.microsoft.com/kb/3155002)
**Служба Mobility Service** | Координирует репликацию между локальными серверами VMware или физическими серверами и Azure или вторичным сайтом.<br/><br/> Устанавливается на виртуальной машине VMware или физических серверах, репликацию которых необходимо выполнить.  | Нет (доступна на портале) | Недоступно
**Агент служб восстановления Microsoft Azure (MARS)** | Координирует репликацию между виртуальными машинами Hyper-V и Azure.<br/><br/> Устанавливается на локальных серверах Hyper-V (с сервером Virtual Machine Manager или без него). | Последняя версия агента ([доступна на портале](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Дальнейшие действия
[Проверьте, соблюдены ли предварительные требования](site-recovery-prereq.md)

