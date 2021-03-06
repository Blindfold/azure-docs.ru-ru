---
title: "Установка SAP HANA на сервере SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт"
description: "Инструкции по установке SAP HANA на сервере SAP HANA в Azure (крупные экземпляры)."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b791be369016dd52d95ec727e46fd8b554c09047
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Как установить и настроить SAP HANA (крупные экземпляры) в Azure

Установку SAP HANA вы выполняете самостоятельно. Это можно сделать сразу после получения нового сервера SAP HANA в Azure (крупные экземпляры) и установки подключения между виртуальными сетями Azure и единицами крупных экземпляров HANA. Обратите внимание, что согласно политике SAP установку SAP HANA должен выполнять пользователь, сертифицированный для установки SAP HANA (сдавший экзамен на установку SAP HANA в рамках сертификации технологического партнера SAP), или системный интегратор с сертификатом SAP.

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>Первые шаги после получения единиц крупных экземпляров HANA

**Первый шаг** после получения крупного экземпляра HANA и установки подключения к экземплярам заключается в регистрации операционной системы экземпляра через поставщика ОС. Этот шаг также включает регистрацию операционной системы в экземпляре SUSE SMT, который необходимо развернуть. Операционную систему RedHat также можно зарегистрировать в диспетчере подписок RedHat, к которому нужно подключиться. Примечания см. в этом [документе](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Кроме того, этот шаг обеспечивает возможность обновления операционной системы в будущем. Это задание выполняется клиентом. 

**Второй шаг** заключается в проверке наличия новых обновлений и исправлений для определенного выпуска или версии операционной системы. Вы должны использовать последнюю версию крупного экземпляра HANA. В зависимости от времени выпуска обновлений и исправлений операционной системы, а также изменений в образе, которые может развернуть Майкрософт, последние исправления могут быть не включены. Поэтому после получения единицы крупного экземпляра HANA и регистрации установки операционной системы через дистрибьютора Linux обязательно проверьте, не выпустил ли определенный поставщик Linux новые исправления системы безопасности, функций, компонентов обеспечения доступности и производительности, которые нужно применить.

**Третий шаг** заключается в ознакомлении с соответствующими примечаниями к SAP по установке и настройке SAP HANA в определенной версии или выпуске операционной системы. Из-за изменения рекомендаций или примечаний к SAP, а также конфигураций, которые зависят от отдельных сценариев установки, Майкрософт не всегда может обеспечить правильную настройку единицы крупного экземпляра HANA. Вы как клиент должны обязательно ознакомиться с примечаниями к SAP (основные из них указаны ниже), проверить настройки выпуска или версии операционной системы и применить отсутствующие параметры конфигурации.

Проверьте приведенные ниже параметры и измените их значения на следующие:

- net.core.rmem_max = 16777216;
- net.core.wmem_max = 16777216;
- net.core.rmem_default = 16777216;
- net.core.wmem_default = 16777216;
- net.core.optmem_max = 16777216;
- net.ipv4.tcp_rmem = 65536 16777216 16777216;
- net.ipv4.tcp_wmem = 65536 16777216 16777216.

Начиная с SLES 12 с пакетом обновления 1 (SP1) и RHEL 7.2, эти параметры необходимо определить в файле конфигурации в каталоге /etc/sysctl.d. Например, в этом каталоге должен находиться файл конфигурации с именем 91-NetApp-HANA.conf. В предыдущих выпусках SLES и RHEL эти параметры необходимо задать в файле /etc/sysctl.conf.

Во всех выпусках RHEL и SLES (начиная с версии 12) параметр 
- sunrpc.tcp_slot_table_entries = 128

необходимо задать в файле /etc/modprobe.d/sunrpc-local.conf. Если этот файл отсутствует, его сначала необходимо создать, добавив следующую запись: 
- options sunrpc tcp_max_slot_table_entries=128.

**Четвертый шаг** заключается в проверке системного времени единицы крупного экземпляра HANA. При развертывании экземпляров в их системе используется часовой пояс региона Azure, в котором расположен стек крупных экземпляров HANA. Вы можете изменить системное время и часовой пояс экземпляров. В этом случае при добавлении новых экземпляров в клиент также необходимо соответствующим образом изменить их часовой пояс. Ресурсы Майкрософт не имеют доступа к сведениям о настройке часового пояса системы в экземплярах после передачи их в эксплуатацию. Поэтому часовой пояс новых экземпляров может отличаться от установленного вами. В результате вы как клиент должны проверить и при необходимости изменить часовой пояс полученных экземпляров. 

**Пятый шаг** заключается в проверке etc/hosts. После передачи в колонках для разных целей назначены отдельные IP-адреса (см. следующий раздел). Проверьте файл etc/hosts. При добавлении единиц в имеющийся клиент в файле etc/hosts новых систем могут быть неправильно настроены IP-адреса предыдущих систем. Вы как пользователь обязаны проверить правильность конфигурации. Это позволит новым экземплярам взаимодействовать с ранее развернутыми единицами в клиенте и разрешать их имена. 

## <a name="networking"></a>Сеть
В этом разделе предполагается, что вы следовали рекомендациям по проектированию виртуальных сетей Azure и их подключению к крупным экземплярам HANA, приведенным в следующих статьях:

- [Обзор и описание архитектуры SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Инфраструктура и возможности подключения SAP HANA в Azure (крупные экземпляры)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

При проектировании сети отдельной единицы следует учитывать некоторые моменты. Каждой единице крупного экземпляра HANA выделяется два или три IP-адреса, которые назначаются соответствующему числу портов сетевого адаптера. Три IP-адреса используются в масштабируемых конфигурациях HANA или в сценарии репликации системы HANA. Один из IP-адресов, назначенных сетевому адаптеру единицы, выделяется из диапазона IP-адресов серверного пула. Дополнительные сведения см. в статье [Обзор и описание архитектуры SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

Распределение для единиц с двумя IP-адресами выполняется следующим образом:

- eth0.xx должен иметь IP-адрес из диапазона IP-адресов серверного пула, отправленного в Майкрософт. Этот IP-адрес нужно указать в файле /etc/hosts операционной системы.
- eth1.xx должен иметь IP-адрес, используемый для взаимодействия с NFS. Таким образом, эти адреса **не** нужно сохранять в файле etc/hosts, чтобы разрешить трафик между экземплярами в одном клиенте.

При развертывании репликации системы HANA или масштабировании HANA конфигурация колонки с двумя назначенными IP-адресами не подходит. При развертывании таких конфигураций обратитесь в службу поддержки Microsoft Service Management, чтобы получить третий IP-адрес в третьей виртуальной локальной сети. В единицах крупных экземпляров HANA при наличии трех назначенных IP-адресов в трех портах сетевого адаптера применяются следующие условия:

- eth0.xx должен иметь IP-адрес из диапазона IP-адресов серверного пула, отправленного в Майкрософт. Поэтому этот IP-адрес не нужно указывать в файле /etc/hosts операционной системы.
- eth1.xx должен иметь IP-адрес, используемый для взаимодействия с хранилищем NFS. Этот тип адресов не нужно указывать в файле etc/hosts.
- eth2.xx должен использоваться исключительно в файле etc/hosts, чтобы обеспечить взаимодействие между разными экземплярами. Эти IP-адреса также применяются в конфигурациях масштабирования HANA как IP-адреса, используемые системой HANA для конфигурации между узлами.



## <a name="storage"></a>Хранилище

Структура хранения для SAP HANA в Azure (крупные экземпляры) настраивается через управление службами SAP HANA в Azure с применением рекомендованных методик, как описано в техническом документе с описанием [требований к хранилищу для SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Ознакомившись с этим документом и посмотрев на единицу крупного экземпляра HANA, вы заметите, что эти единицы имеют крупный том диска HANA/data, а также наличие тома HANA/log/backup. Причина, почему мы выделили больше места для HANA/data, заключается в том, что на этот том диска также сохраняются моментальные снимки, созданные с помощью предоставленной нами функции. Это означает, что чем больше моментальных снимком вы сделали, тем больше требуется пространства. Том HANA/log/backup не используется для хранения резервных копий базы данных. Это архивный том для журнала транзакций HANA. В будущих версиях функции самостоятельного создания моментальных снимков хранилища мы планируем настроить сохранение моментальных снимков на этот том. Это позволит создавать моментальные снимки чаще и при этом чаще выполнять репликацию на сайт аварийного восстановления при использовании функции аварийного восстановления, предоставленной инфраструктурой крупных экземпляров HANA. Дополнительные сведения см. в статье [Высокий уровень доступности и аварийное восстановление SAP HANA в Azure (крупные экземпляры)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Кроме предоставленного хранилища, вы можете приобрести дополнительную емкость хранилища с шагом в 1 ТБ. Это дополнительное хранилище добавляется к HANA (крупные экземпляры) как новые тома.

Во время адаптации с Microsoft Service Management пользователи указывают идентификатор пользователя <SID>ADM и группы sapsys (например, 1000,500). Важно, чтобы при установке системы SAP HANA использовались те же значения.

Контроллер хранилища и узлы в стеках больших экземпляров синхронизируются с NTP-серверами. Если выполняется синхронизация с NTP-сервером для единиц SAP HANA в Azure (крупные экземпляры) и виртуальных машин Azure, не должно быть заметного смещения времени между инфраструктурой и вычислительными единицами в Azure или стеками крупных экземпляров.

Чтобы оптимизировать хранилище, используемое SAP HANA, необходимо также задать следующие параметры конфигурации:

- max_parallel_io_requests 128;
- async_read_submit on;
- async_write_submit_active on;
- async_write_submit_blocks all.
 
Начиная с версии SAP HANA 1.0 и до SPS 12, эти параметры можно указать во время установки базы данных SAP HANA, как описано в [примечании к SAP 2267798](https://launchpad.support.sap.com/#/notes/2267798).

Эти параметры также можно настроить после установки базы данных SAP HANA с помощью средства hdbparam. 

Это средство не поддерживается в системе SAP HANA 2.0. В этом случае эти параметры необходимо задать с помощью команд SQL. Дополнительные сведения см. в [примечании к SAP 2399079 об устранении hdbparam в HANA 2.0](https://launchpad.support.sap.com/#/notes/2399079).


## <a name="operating-system"></a>операционная система

Для SAP HANA в Azure (большие экземпляры) используется дистрибутив [SUSE Linux Enterprise Server 12 SP1 для приложений SAP](https://www.suse.com/products/sles-for-sap/hana). Этот дистрибутив предоставляет все необходимые для SAP функции &quot;без дополнительной настройки&quot; (в том числе предварительно настроенные параметры для эффективного запуска SAP на сервере SLES).

В разделе [библиотеки ресурсов и технической документации](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) на веб-сайте SUSE и на [странице SAP для SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) в сетевом сообществе SAP (SCN) доступно несколько полезных ресурсов, в которых описывается развертывание SAP HANA на SLES (в том числе настройки высокой доступности, методы обеспечения безопасности для работы SAP и многое другое).

Дополнительная информация и полезные ссылки, имеющие отношение к SLES:

- [SAP HANA на сайте SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE);
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Лучшие рекомендации для SAP: создание очереди репликации — SAP NetWeaver на SUSE Linux Enterprise 12);
- [ClamSAP – защита от вирусов на SLES для SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (включая SLES 12 для приложений SAP).

Примечания по поддержке SAP, применимые к внедрению SAP HANA на SLES 12 SP1:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Примечание по поддержке SAP № 1944799 — Рекомендации для установки SAP HANA на ОС SLES);
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Примечание по поддержке SAP № 2205917 — рекомендуемые параметры ОС для SLES 12 для приложений SAP);
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Примечание по поддержке SAP № 1984787 — примечания по установке SUSE LINUX Enterprise Server 12);
- [SAP Support Note #171356 – SAP Software on Linux: General Information](https://launchpad.support.sap.com/#/notes/1984787) (Примечание по поддержке № 171356 — общие сведения о ПО SAP на платформе Linux);
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Примечание по поддержке № 1391070 — решения UUID для Linux).

## <a name="time-synchronization"></a>Синхронизация времени

Системы SAP очень чувствительны к разнице системного времени на разных компонентах. Если вы уже давно работаете с базовыми системами SAP, вам должны быть знакомы короткие дампы ABAP SAP с ошибкой, озаглавленной ZDATE\_LARGE\_TIME\_DIFF. Они появляются именно в том случае, когда системное время на разных серверах или виртуальных машинах существенно различается.

Для SAP HANA в Azure (крупные экземпляры) синхронизация времени в Azure не применяется к вычислительным единицам в стеках больших экземпляров. Это не относится к приложениям SAP, выполняемым в Azure обычным образом (на виртуальных машинах), так как Azure гарантирует правильную синхронизацию системного времени. Соответственно, потребуется отдельный сервер времени, который будут использовать серверы приложений SAP, работающие на виртуальных машинах Azure, и экземпляры баз данных SAP HANA, работающие на HANA (крупные экземпляры). Для инфраструктуры хранения в стеках крупных экземпляров время синхронизируется с серверами NTP.



