---
title: "Перенос классической виртуальной машины в виртуальную машину ARM с управляемыми дисками | Документация Майкрософт"
description: "Миграция отдельной виртуальной машины из классической модели развертывания на Управляемые диски Azure в модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 3a3730821b88062fdccf18732630be0bcb6ae7a7
ms.lasthandoff: 04/07/2017


---

# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Ручная миграция классической виртуальной машины в новую виртуальную машину ARM с управляемыми дисками с помощью VHD 


Этот раздел поможет перенести имеющиеся виртуальные машины из классической модели развертывания на [Управляемые диски](../../storage/storage-managed-disks-overview.md) в модели развертывания с помощью Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Планирование миграции на управляемые диски

Этот раздел поможет выбрать соответствующие виртуальные машины и типы дисков.


### <a name="location"></a>Расположение

Выберите расположение, в котором доступны Управляемые диски Azure. Если миграция выполняется на управляемые диски уровня "Премиум", то в этом регионе должно быть также доступно хранилище уровня "Премиум". Актуальные сведения о поддерживаемых расположениях см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/regions/#services).

### <a name="vm-sizes"></a>Размеры виртуальной машины

Если миграция выполняется на управляемые диски уровня "Премиум", то необходимо выбрать для виртуальной машины размер, поддерживающий хранилище уровня "Премиум", из числа доступных в регионе размещения виртуальной машины. Изучите размеры виртуальных машин, чтобы выбрать подходящий для хранилища уровня "Премиум". Спецификации размеров виртуальных машин Azure перечислены в статье [Размеры виртуальных машин](sizes.md).
Просмотрите характеристики виртуальных машин, которые работают с хранилищем класса Premium, и выберите размер виртуальной машины, наиболее подходящий для вашей рабочей нагрузки. Убедитесь, что виртуальная машина имеет достаточную пропускную способность для поддержки трафика диска.

### <a name="disk-sizes"></a>Размеры диска

**Управляемые диски уровня "Премиум"**

Существует три типа управляемых дисков уровня "Премиум", которые можно использовать с виртуальной машиной. Для каждого из них действуют определенные ограничения на пропускную способность и число операций ввода-вывода в секунду. Учитывайте эти ограничения при выборе типа диска уровня "Премиум" для виртуальной машины в зависимости от потребностей приложения с точки зрения емкости, производительности, масштабируемости и пиковых нагрузок.

| Диски уровня "Премиум"  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Размер диска           | 128 ГБ            | 512 ГБ            | 1024 ГБ (1 ТБ)    |
| Количество операций ввода-вывода в секунду на диск       | 500               | 2300              | 5000              |
| Пропускная способность на диск | 100 МБ в секунду | 150 МБ в секунду | 200 МБ в секунду |

**Управляемые диски уровня "Стандартный"**

Существует пять типов управляемых дисков, которые можно использовать с виртуальной машиной. У них разная емкость, но одинаковые ограничения пропускной способности и числа операций ввода-вывода в секунду. Выберите тип управляемых дисков уровня "Стандартный" в зависимости от потребностей в емкости своего приложения.

| Диски уровня "Стандартный"  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Размер диска           | 30 ГБ            | 64 ГБ            | 128 ГБ           | 512 ГБ           | 1024 ГБ (1 ТБ)   |
| Количество операций ввода-вывода в секунду на диск       | 500              | 500              | 500              | 500              | 500              |
| Пропускная способность на диск | 60 МБ в секунду | 60 МБ в секунду | 60 МБ в секунду | 60 МБ в секунду | 60 МБ в секунду |

### <a name="disk-caching-policy"></a>Политика кэширования дисков 

**Управляемые диски уровня "Премиум"**

По умолчанию для политики кэширования дисков задано значение *только чтение* для всех дисков с данными "Премиум" и *чтение и запись* для дисков операционной системы "Премиум", подключенных к виртуальной машине. Этот параметр конфигурации рекомендуется использовать, чтобы достичь оптимальной производительности при операциях ввода-вывода приложения. Отключите кэширование дисков данных, которые отличаются высокой интенсивностью записи или используются только для записи (например, файлов журналов SQL Server), чтобы улучшить производительность приложения.

### <a name="pricing"></a>Цены

Ознакомьтесь с [ценами на Управляемые диски](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Цены на управляемые диски уровня "Премиум" совпадают с ценами на неуправляемые диски уровня "Премиум". При этом цены на управляемые диски уровня "Стандартный" отличаются от цен на неуправляемые диски уровня "Стандартный".


## <a name="checklist"></a>Контрольный список

1.  При миграции на управляемые диски уровня "Премиум" убедитесь, что они доступны в выбранном регионе.

2.  Определите серию виртуальной машины, которую вы будете использовать. Она должна поддерживать хранилище уровня "Премиум", если осуществляется миграция на управляемые диски уровня "Премиум".

3.  Выберите точный размер виртуальной машины, который доступен в регионе, выбранном для миграции. Размер виртуальной машины должен быть достаточно большим, чтобы поддерживать количество дисков данных, которые вы используете. Например, при наличии четырех дисков данных виртуальная машина должна иметь два или больше ядер. Кроме того, учитывайте требования к вычислительной мощности, памяти и пропускной способности сети.

4.  Держите сведения о текущей виртуальной машине под рукой, в том числе список дисков и соответствующих BLOB-объектов виртуальных жестких дисков.

Подготовьте приложение к простою. Чтобы выполнить чистый перенос, необходимо остановить все процессы, выполняющиеся в текущей системе. Только так вы получите стабильное состояние, которое можно перенести на новую платформу. Длительность простоя зависит от объема данных на переносимых дисках.


## <a name="migrate-the-vm"></a>Миграция виртуальной машины

Подготовьте приложение к простою. Чтобы выполнить чистый перенос, необходимо остановить все процессы, выполняющиеся в текущей системе. Только так вы получите стабильное состояние, которое можно перенести на новую платформу. Длительность простоя зависит от объема данных на переносимых дисках.


1.  Сначала задайте общие параметры.

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Создайте управляемый диск ОС с помощью виртуального жесткого диска из классической виртуальной машины.

    Обязательно укажите полный универсальный код ресурса (URI) виртуального жесткого диска ОС в параметре $osVhdUri. Также для параметра **-AccountType** укажите значение **PremiumLRS** или **StandardLRS**, в зависимости от типа дисков ("Премиум" или "Стандартный"), на которые выполняется миграция.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Подключите диск ОС к новой виртуальной машине.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Создайте управляемый диск данных из VHD-файла данных и добавьте его в новую виртуальную машину.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Создайте новую виртуальную машину, настроив ее общедоступный IP-адрес, виртуальную сеть и сетевую карту.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Чтобы обеспечить поддержку приложений, может потребоваться выполнить дополнительные шаги, не описанные в этом руководстве.
>
>

## <a name="next-steps"></a>Дальнейшие действия

- Подключитесь к виртуальной машине. Указания см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти в систему](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


