---
title: "Настройка Oracle ASM в виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Быстрая подготовка и запуск Oracle ASM в среде Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e3c98a3d4d7012f6cb6c7e1a1b8263e6ecdee57b
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017

---

# <a name="set-up-oracle-asm-on-an-azue-linux-virtual-machine"></a>Настройка Oracle ASM в виртуальной машине Linux в Azure 

Из этой статьи вы узнаете, как установить и настроить Oracle Automatic Storage Management (Oracle ASM) на виртуальной машине Oracle Linux в Azure.

Прежде чем начать, убедитесь, что установлен Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Вход в Azure 

Для входа в подписку Azure в Azure CLI используйте команду [az login](/cli/azure/#login). Затем выполните инструкции на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Чтобы создать группу ресурсов, используйте команду [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем myResourceGroup в расположении westus.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Создание виртуальной машины

Чтобы создать виртуальную машину для использования с Oracle ASM, сделайте следующее:

1.  Чтобы создать виртуальную машину, выполните команду [az vm create](/cli/azure/vm#create). 

  В следующем примере создаются виртуальная машина с именем myVM и ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`.  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
  ```

  После создания виртуальной машины в Azure CLI отображается информация следующего вида. Обратите внимание на значение `publicIpAddress`. Этот адрес используется для доступа к виртуальной машине.

  ```azurecli
  {
    "fqdns": "",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
    "location": "westus",
    "macAddress": "00-0D-3A-36-2F-56",
    "powerState": "VM running",
    "privateIpAddress": "10.0.0.4",
    "publicIpAddress": "13.64.104.241",
    "resourceGroup": "myResourceGroup"
  }
  ```

2.  Добавьте диски для использования с конфигурацией Oracle ASM:

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

## <a name="connect-to-the-vm"></a>Подключение к виртуальной машине

Используйте следующую команду для создания сеанса SSH с виртуальной машиной. Замените IP-адрес общедоступным IP-адресом виртуальной машины (значение `publicIpAddress`).

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Установка Oracle ASM

Чтобы установить ASM, сделайте следующее: 

Дополнительные сведения об установке Oracle ASM см. в статье [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html) (Скачиваемые компоненты Oracle ASMLib для Oracle Linux 6).  

1. Выполните команду `yum list`:

  ```bash
  $ sudo su -
  # yum list
  ```
  Для загрузки `yum list` в первый раз может понадобиться несколько минут.

2.  Выполните следующие дополнительные команды:

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

3.  Убедитесь, что служба Oracle ASM установлена:

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

4.  Добавьте пользователей и группы:

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

5.  Проверьте пользователей и группы:

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

6.  Создайте папку и измените владельца:

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>Настройка Oracle ASM

В этом руководстве пользователем по умолчанию является *grid*, а группой по умолчанию — *asmadmin*. Убедитесь, что пользователь *oracle* входит в группу asmadmin. Чтобы настроить установку Oracle ASM, сделайте следующее:

1.  Установите драйвер библиотеки Oracle ASM:

  ```bash
  # /usr/sbin/oracleasm configure -i

  Configuring the Oracle ASM library driver.

  This will configure the on-boot properties of the Oracle ASM library
  driver. The following questions will determine whether the driver is
  loaded on boot and what permissions it will have. The current values
  will be shown in brackets ('[]'). Hitting <ENTER> without typing an
  answer will keep that current value. Ctrl-C will abort.

  Default user to own the driver interface []: grid
  Default group to own the driver interface []: asmadmin
  Start Oracle ASM library driver on boot (y/n) [n]: y
  Scan for Oracle ASM disks on boot (y/n) [y]: y
  Writing Oracle ASM library driver configuration: done
  ```

2.  Просмотрите конфигурацию диска:
  ```bash
  # cat /proc/partitions
  ```

3.  Отформатируйте диск:

  ```bash
  # fdisk /dev/sdc
  Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
  Building a new DOS disklabel with disk identifier 0xf865c6ca.
  Changes will remain in memory only, until you decide to write them.
  After that, of course, the previous content won't be recoverable.

  Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

  The device presents a logical sector size that is smaller than
  the physical sector size. Aligning to a physical sector (or optimal
  I/O) size boundary is recommended, or performance may be impacted.

  WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
          switch off the mode (command 'c') and change display units to
          sectors (command 'u').

  Command (m for help): n
  Command action
    e   extended
    p   primary partition (1-4)
  p
  Partition number (1-4): 1
  First cylinder (1-6527, default 1):
  Using default value 1
  Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
  Using default value 6527

  Command (m for help): w
  The partition table has been altered!

  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

4.  Повторите предыдущий шаг для /dev/sdd, /dev/sde и /dev/sdf.

5.  Проверьте конфигурацию диска:

  ```bash
  # cat /proc/partitions
  major minor  #blocks  name

    8       16   14680064 sdb
    8       17   14678976 sdb1
    8       32   52428800 sdc
    8       33   52428096 sdc1
    8       48   52428800 sdd
    8       49   52428096 sdd1
    8       64   52428800 sde
    8       65   52428096 sde1
    8       80   52428800 sdf
    8       81   52428096 sdf1
    8        0   52428800 sda
    8        1     512000 sda1
    8        2   51915776 sda2
    11        0    1048575 sr0
  ```

6.  Проверьте состояние службы Oracle ASM:

  ```bash
  # service oracleasm status
  Checking if ASM is loaded: no
  Checking if /dev/oracleasm is mounted: no
  ```

7.  Запустите службу Oracle ASM:

  ```bash
  # service oracleasm start
  Initializing the Oracle ASMLib driver:                     [  OK  ]
  Scanning the system for Oracle ASMLib disks:               [  OK  ]
  ```

8.  Создайте диски Oracle ASM:

  ```bash
  # service oracleasm createdisk ASMSP /dev/sdc1
  Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

  # service oracleasm createdisk DATA /dev/sdd1
  Marking disk "DATA" as an ASM disk:                        [  OK  ]

  # service oracleasm createdisk DATA1 /dev/sde1
  Marking disk "DATA1" as an ASM disk:                       [  OK  ]

  [root@myVM ~]# service oracleasm createdisk FRA /dev/sdf1
  Marking disk "FRA" as an ASM disk:                         [  OK  ]
  ```

9.  Выведите список дисков Oracle ASM:

  ```bash
  # service oracleasm listdisks
  ASMSP
  DATA
  DATA1
  FRA
  ```

10. Измените пароли для пользователей root, oracle и grid (пароли будут использоваться позже во время установки):

  ```bash
  # passwd oracle
  # passwd grid
  # passwd root
  ```

11. Измените разрешение папки:

  ```bash
  # chmod -R 775 /opt
  # chown grid:oinstall /opt
  # chown oracle:oinstall /dev/sdc1
  # chown oracle:oinstall /dev/sdd1
  # chown oracle:oinstall /dev/sde1
  # chown oracle:oinstall /dev/sdf1
  # chmod 600 /dev/sdc1
  # chmod 600 /dev/sdd1
  # chmod 600 /dev/sde1
  # chmod 600 /dev/sdf1
  ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Скачивание и подготовка Oracle Grid Infrastructure

Чтобы скачать и подготовить программное обеспечение Oracle Grid Infrastructure, сделайте следующее:

1.  Скачайте Oracle Grid Infrastructure со [страницы скачивания Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

  Под заголовком **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) для Linux x86–64** должно быть два ZIP-файла для скачивания.

2.  Скачав ZIP-файлы на клиентском компьютере, вы можете скопировать файлы на виртуальную машину по протоколу SCP.

    ```bash
    scp *.zip <publicIpAddress>:<folder>
    ```

3.  Переместите ZIP-файлы в папку /opt. Затем измените владельца файлов:

    ```bash
    # mv <folder>/*.zip /opt
    # cd /opt
    # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
    # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
    ```

4.  Распакуйте файлы (установите служебную программу Linux для распаковки, если она еще не установлена):

    ```bash
    # yum install unzip
    # unzip linuxamd64_12102_grid_1of2.zip
    # unzip linuxamd64_12102_grid_2of2.zip
    ```

5.  Измените разрешение:

    ```bash
    # chown -R grid:oinstall /opt/grid
    ```

6.  Отключите брандмауэр:

    ```bash
    # service iptables status
    # service iptables stop
    ```

7.  Проверьте доступную область буфера. Требуется по крайней мере 6 ГБ области буфера для установки Oracle Grid Infrastructure:

    ```bash
    # swapon -s
    ```

    Если доступно менее 6 ГБ, вы можете увеличить область буфера, выполнив следующие команды:

    ```bash
    # dd if=/dev/zero of=/extraswap bs=1M count=6144
    6144+0 records in
    6144+0 records out
    6442450944 bytes (6.4 GB) copied, 141.245 s, 45.6 MB/s

    # mkswap /mnt/resource/extraswap
    mkswap: /mnt/resource/extraswap: warning: don't erase bootbits sectors
            on whole disk. Use -f to force.
    Setting up swapspace version 1, size = 6291452 KiB
    no label, UUID=80bd7816-b3a2-4eec-a824-733209644fc5
    # swapon /mnt/resource/extraswap

    ```

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Подготовка клиента и виртуальной машины для запуска X11 (только для клиентов Windows)

Чтобы подготовить клиент и виртуальную машину для запуска X11, сделайте следующее: 

1.  Выполните вход в качестве пользователя root, а затем измените файл /etc/ssh/ssh_config. Измените параметр для Forwardx11 на `yes`:

  ```
  #   ForwardX11 no
  ForwardX11 yes

  ```

2.  Скачайте PuTTY и Xming на компьютер с Windows:

  * [Скачать PuTTY](http://www.putty.org/).
  * [Скачать Xming](https://xming.en.softonic.com/).

3.  Установив PuTTY в папке PuTTY (например, C:\Program Files\PuTTY), запустите файл puttygen.exe (генератор ключей PuTTY).

4.  В генераторе ключей PuTTY сделайте следующее:

  1.  Чтобы создать ключ, нажмите кнопку **Создать**. 
  2.  Скопируйте содержимое ключа (CTRL+C).
  3.  Нажмите кнопку **Save private key** (Сохранить закрытый ключ). 
  4.  Игнорируйте предупреждение, появившееся на экране, и нажмите кнопку **ОК**.

  ![Снимок экрана со страницей генератора ключей PuTTY](./media/asm-configuration/puttykeygen.png)

5.  В виртуальной машине выполните следующие команды:

  ```bash
  # sudo su - grid
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

6.  Создайте файл с именем authorized_keys. Вставьте содержимое ключа в этот файл и сохраните файл.

  > [!NOTE]
  > Ключ должен содержать строку `ssh-rsa`. Кроме того, содержимое ключа должно быть одной строкой текста.
  >  

7.  Запустите PuTTY. В области **Категория** выберите **Подключение** > **SSH** > **Проверка подлинности**. В поле **Private key file for authentication** (Файл закрытого ключа для проверки подлинности) выберите созданный ранее ключ.

  ![Снимок экрана со страницей настройки закрытого ключа](./media/asm-configuration/setprivatekey.png)

9.  В области **Категория** выберите **Подключение** > **SSH** > **X11**. Установите флажок **Enable X11 forwarding** (Включить перенаправление X11).

  ![Снимок экрана со страницей включения X11](./media/asm-configuration/enablex11.png)

10. В области **Категория** выберите **Сеанс**. Введите сведения об узле, а затем нажмите кнопку **Открыть**.

  ![Снимок экрана со страницей "Сеанс"](./media/asm-configuration/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Установка Oracle Grid Infrastructure

Чтобы установить Oracle Grid Infrastructure, сделайте следующее:

1. Выполните вход в качестве пользователя grid. Вы сможете войти, не вводя пароль. 

  > [!NOTE]
  > Перед установкой убедитесь, что Xming работает.

    ```bash
    $ cd /opt/grid
    $ ./runInstaller
    ```

  Откроется установщик Oracle Grid Infrastructure 12c Release 1. Запуск установщика может занять несколько минут.

2. На странице **выбора варианта установки** выберите **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Установить и настроить Oracle Grid Infrastructure для автономного сервера).

  ![Снимок экрана со страницей выбора варианта установки в установщике](./media/asm-configuration/install01.png)

3. На странице **выбора языка продукта** выберите **английский** или другой нужный язык.

  ![Снимок экрана со страницей выбора языка продукта в установщике](./media/asm-configuration/install02.png)

4. На странице **Create ASM Disk Group** (Создание группы дисков ASM) сделайте следующее:
  1.  Введите имя группы дисков.
  2.  В разделе **Redundancy** (Избыточность) выберите **External** (Внешняя).
  3.  В списке **Allocation Unit Size** (Размер единицы распределения) выберите значение **4**.
  4.  В разделе **Add Disks** (Добавление дисков) выберите **ORCLASMSP**.

  ![Снимок экрана со страницей создания группы дисков ASM в установщике](./media/asm-configuration/install03.png)

5. На странице **указания пароля ASM** установите переключатель **Use same passwords for these accounts** (Использовать одинаковые пароли для этих учетных записей) и введите пароль.

  ![Снимок экрана со страницей указания пароля ASM в установщике](./media/asm-configuration/install04.png)

6. (Необязательно.) На странице **указания параметров управления** установите флажок **Register with Enterprise Manager (EM) Cloud Control** (Зарегистрироваться с помощью Enterprise Manager (EM) Cloud Control).

  ![Снимок экрана со страницей указания параметров управления в установщике](./media/asm-configuration/install05.png)

7. На странице **привилегированных групп ОС** используйте параметры по умолчанию.

  ![Снимок экрана со страницей привилегированных групп ОС в установщике](./media/asm-configuration/install06.png)

8. На странице **указания расположения установки** используйте параметры по умолчанию.

  ![Снимок экрана со страницей указания расположения установки в установщике](./media/asm-configuration/install07.png)

9. На странице **создания инвентаризации** введите расположение папки или перейдите к нему.

  ![Снимок экрана со страницей создания инвентаризации в установщике](./media/asm-configuration/install08.png)

10. На странице **конфигурации выполнения корневого скрипта** установите флажок **Automatically run configuration scripts** (Запускать скрипты настройки автоматически). Затем установите флажок **Use "root" user credential** (Использовать учетные данные привилегированного пользователя) и введите пароль привилегированного пользователя.

  ![Снимок экрана со страницей конфигурации выполнения корневого скрипта в установщике](./media/asm-configuration/install09.png)

11. На странице **выполнения проверки предварительных требований** нажмите кнопку **Fix & Check Again** (Внести исправления и продолжить проверку).

  ![Снимок экрана со страницей выполнения проверки предварительных требований в установщике](./media/asm-configuration/install10.png)

12. На странице **скрипта исправления** нажмите кнопку **ОК**.

  ![Снимок экрана со страницей скрипта исправления в установщике](./media/asm-configuration/install11.png)

13. На странице **сводки** просмотрите выбранные параметры и нажмите кнопку **Установить**.

  ![Снимок экрана со страницей сводки в установщике](./media/asm-configuration/install12.png)

14. Появится диалоговое окно с предупреждением. Чтобы продолжить, нажмите кнопку **Да**.

  ![Снимок экрана с диалоговым окном, содержащим предупреждение](./media/asm-configuration/install14.png)

15. На странице **завершения** нажмите кнопку **Закрыть** для завершения установки.

  ![Снимок экрана со страницей завершения в установщике](./media/asm-configuration/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Настройка установки Oracle ASM

Чтобы настроить установку Oracle ASM, сделайте следующее:

1.  В сеансе X11 выполните вход в качестве пользователя grid:

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  Откроется Oracle ASM Configuration Assistant.

2.  На странице **Configure ASM: Disk Groups** (Настройка ASM: группы дисков) нажмите кнопку **Создать**, а затем выберите параметр **Show Advanced Options** (Показать расширенные параметры).

  ![Снимок экрана со страницей Configure ASM: Disk Groups (Настройка ASM: группы дисков)](./media/asm-configuration/asm01.png)

3.  На странице **Create Disk Group** (Создание группы дисков) сделайте следующее:

  1.  Введите имя группы дисков.
  2.  В разделе **Select Member Disks** (Выбор дисков-участников) выберите **ORCL_DATA** и **ORCL_DATA1**.
  3.  В списке **Allocation Unit Size** (Размер единицы распределения) выберите значение **4**. 

  ![Снимок экрана со страницей Create Disk Group (Создание группы дисков)](./media/asm-configuration/asm02.png)

4.  На странице **Configure ASM: Disk Groups** (Настройка ASM: группы дисков) нажмите кнопку **Создать**, а затем выберите параметр **Show Advanced Options** (Показать расширенные параметры).

  ![Снимок экрана со страницей Configure ASM: Disk Groups (Настройка ASM: группы дисков)](./media/asm-configuration/asm03.png)

5.  На странице **Create Disk Group** (Создание группы дисков) сделайте следующее:

  1.  Введите имя группы дисков.
  2.  В разделе **Redundancy** (Избыточность) выберите **External** (Внешняя).
  3.  В разделе **Select Member Disks** (Выбор дисков-участников) выберите **ORCL_FRA**.
  4.  В списке **Allocation Unit Size** (Размер единицы распределения) выберите значение **4**.

  ![Снимок экрана со страницей Create Disk Group (Создание группы дисков)](./media/asm-configuration/asm04.png)

6.  Нажмите кнопку **Выйти**, чтобы закрыть ASM Configuration Assistant.

  ![Снимок экрана со страницей Configure ASM: Disk Groups (Настройка ASM: группы дисков) с кнопкой "Выйти"](./media/asm-configuration/asm05.png)

## <a name="create-the-database"></a>Создание базы данных

Программное обеспечение Oracle уже установлено в образе Azure Marketplace. Чтобы установить базу данных, сделайте следующее:

1.  Переключитесь на суперпользователя oracle и инициализируйте прослушиватель для ведения журнала:

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   Откроется Database Configuration Assistant.

2.  На странице **Database Operation** (Операция с базой данных) выберите **Create Database** (Создать базу данных).

  ![Снимок экрана со страницей Database Operation (Операции базы данных)](./media/asm-configuration/createdb01.png)
  
3. На странице **Creation Mode** (Режим создания) сделайте следующее:

    1.  Введите имя для базы данных. 
    2.  В списке **Storage Type** (Тип хранилища) выберите **Automatic Storage Management (ASM)**.
    3.  В поле **Database Files Location** (Расположение файлов базы данных) перейдите к нужной папке.
    4.  В поле **Fast Recovery Area** (Область быстрого восстановления) перейдите к нужной папке.

  ![Снимок экрана со страницей Creation Mode (Режим создания)](./media/asm-configuration/createdb02.png)

4.  На странице **сводки** просмотрите выбранные параметры и нажмите кнопку **Готово**, чтобы создать базу данных.

  ![Снимок экрана со страницей сводки](./media/asm-configuration/createdb03.png)

5.   (Необязательно.) На странице **завершения** для изменения пароля нажмите кнопку **Password Management** (Управление паролями).

  ![Снимок экрана со страницей завершения](./media/asm-configuration/createdb04.png)


## <a name="delete-the-vm"></a>Удаление виртуальной машины

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, использовав следующую команду:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

[Создание полной среды Linux с помощью Azure CLI 2.0](../../linux/create-cli-complete.md)

[Примеры Azure CLI для виртуальных машин Linux](../../linux/cli-samples.md).

