---
title: "Создание виртуальной машины с помощью универсального виртуального жесткого диска | Документация Майкрософт"
description: "Узнайте, как создать виртуальную машину Windows с помощью универсального образа VHD из учетной записи хранения, используя Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 537f9878a22a90a55f30b0b4cee766017e753f37
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>Создание виртуальной машины с помощью универсального образа VHD из учетной записи хранения 

В этом разделе описывается создание виртуальной машины на основе универсального неуправляемого диска, расположенного в учетной записи хранения. С универсального образа VHD удалены все сведения вашей личной учетной записи с помощью [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Чтобы создать универсальный диск VHD, запустите программу Sysprep на локальной виртуальной машине, а затем [загрузите VHD в Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Также можно запустить программу Sysprep на существующей виртуальной машине Azure, а затем [скопировать VHD](vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Если необходимо создать виртуальную машину из специализированного VHD, расположенного в учетной записи хранения, ознакомьтесь с разделом [Создание виртуальной машины на основе специализированного VHD](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Сведения об использовании Управляемых дисков вместо дисков, расположенных в учетной записи хранения, приведены в разделах [Запись управляемого образа универсальной виртуальной машины в Azure](capture-image-resource.md) и [Создание виртуальной машины из универсального управляемого образа виртуальной машины](create-vm-generalized-managed.md).

## <a name="prerequisites"></a>Предварительные требования
Если вы собираетесь использовать диск VHD, переданный с локальной виртуальной машины, как созданный с помощью Hyper-V, обязательно выполните инструкции из статьи [Подготовка виртуального жесткого диска Windows к передаче в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Перед созданием виртуальной машины с помощью этого метода как переданные диски VHD, так и существующие диски VHD виртуальной машины Azure необходимо обобщить. Дополнительные сведения см. в разделе [Generalize a Windows virtual machine using Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Обобщение виртуальной машины Windows с помощью Sysprep). 

## <a name="set-the-uri-of-the-vhd"></a>Указание универсального кода ресурса (URI) диска VHD

Код URI, который должен использовать диск VHD, имеет такой формат: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. В этом примере диск VHD с именем **myVHD** находится в учетной записи хранения **mystorageaccount** в контейнере **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Создание подсети. В следующем примере создается подсеть с именем **mySubnet** в группе ресурсов **myResourceGroup** с префикс адреса **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Создание виртуальной сети. В следующем примере создается виртуальная сеть с именем **myVnet** в расположении **Запад США** с префиксом адреса **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Создание общедоступного IP-адреса и сетевого интерфейса
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создание общедоступного IP-адреса. В этом примере создается общедоступный IP-адрес с именем **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Создание сетевой карты. В этом примере создается сетевая карта с именем **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP
Чтобы войти на виртуальную машину с помощью RDP, необходимо настроить правило безопасности, которое разрешает доступ RDP через порт 3389. 

В этом примере создается группа безопасности сети с именем **myNsg**, которая содержит правило с именем **myRdpRule**, разрешающее трафик RDP через порт 3389. Дополнительные сведения о группах безопасности сети см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Создание переменной для виртуальной сети
Создайте переменную для готовой виртуальной сети. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Создание виртуальной машины
Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования переданного образа виртуальной машины в качестве источника новой установки.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите элементы **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения об управлении новыми виртуальными машинами с помощью PowerShell см. в статье [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


