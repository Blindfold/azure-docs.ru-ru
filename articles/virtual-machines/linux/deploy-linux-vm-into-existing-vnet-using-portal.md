---
title: "Развертывание виртуальных машин Linux в существующих сетях с помощью портала Azure | Документация Майкрософт"
description: "Разверните виртуальную машину Linux в существующей виртуальной сети Azure с помощью портала."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 27704aa20a4514cfca200e70ffcc894059207281
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>Развертывание виртуальной машины Linux в существующей виртуальной сети и группе безопасности сети с помощью портала

В этой статье показано, как развернуть виртуальную машину в существующей виртуальной сети.  Мы рекомендуем, чтобы ресурсы Azure, такие как виртуальные сети и группы безопасности сети, были статическими, хранились в течение продолжительного времени и редко развертывались.  После развертывания виртуальной сети ее можно повторно использовать при постоянных повторных развертываниях. Инфраструктура при этом не пострадает.  Воспринимайте виртуальную сеть в качестве традиционного аппаратного сетевого коммутатора. Вам не понадобится настраивать новый аппаратный коммутатор при каждом развертывании.  

Если виртуальная сеть правильно настроена, мы можем развертывать в ней новые серверы снова и снова, внося некоторые изменения во время ее использования (если они нужны).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала мы развернем группу ресурсов для организации всех компонентов, которые будут созданы в этом пошаговом руководстве.  Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Создание виртуальной сети

Сначала нужно создать виртуальную сеть для запуска виртуальных машин.  Виртуальная сеть содержит одну подсеть. В дальнейшем мы свяжем группу безопасности сети с этой подсетью.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Добавление виртуальной сетевой карты в подсеть

Виртуальные сетевые карты играют важную роль, так как их можно подключать к разным виртуальным машинам. Таким образом виртуальная сетевая карта является статическим ресурсом, а виртуальные машины могут быть временными. Создайте виртуальную сетевую карту и свяжите ее с подсетью, созданной на предыдущем шаге.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>Создание группы безопасности сети

На уровне сети группы безопасности сети Azure эквивалентны брандмауэру. Дополнительные сведения о группах безопасности Azure см. в [этой статье](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Добавление правила, разрешающего входящий трафик по протоколу SSH

Виртуальной машине Linux требуется доступ к Интернету, поэтому создается правило, разрешающее передачу входящего трафика в сети через порт 22 на виртуальную машину Linux.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Связывание группы безопасности сети с подсетью

Создав виртуальную сеть и подсеть, мы свяжем группу безопасности сети с подсетью.  Группы безопасности сети можно связать со всей подсетью или с отдельной виртуальной сетевой картой.  Брандмауэр фильтрует трафик на уровне подсети, а группа безопасности сети защищает все виртуальные сетевые карты и виртуальные машины в подсети. Если бы группа безопасности сети была связана только с одной виртуальной сетевой картой, она бы обеспечивала защиту только одной виртуальной машины.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Развертывание виртуальной машины в виртуальной сети и группе безопасности сети

С помощью Azure CLI можно развернуть виртуальную машину Linux в существующей группе ресурсов Azure, виртуальной сети, подсети и виртуальной сетевой карте.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

Используя портал для выбора существующих ресурсов, мы укажем среде Azure развернуть виртуальную машину в существующей сети.  После развертывания виртуальную сеть и подсеть можно оставить в качестве статических или постоянных ресурсов в регионе Azure для повторного развертывания.  

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание виртуальных машин и управление ими с помощью шаблонов диспетчера ресурсов Azure и интерфейса командной строки Azure](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание полной среды Linux с помощью интерфейса командной строки Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Создание виртуальной машины Linux с помощью шаблона Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

