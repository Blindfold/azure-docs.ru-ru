---
title: "Создание пакета для приложения Azure Service Fabric | Документация Майкрософт"
description: "Как упаковать приложение Service Fabric перед его развертыванием в кластере."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/24/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 15b6f6c85c5a5accbd31225c277de87346a2e16f
ms.lasthandoff: 04/27/2017


---
# <a name="package-an-application"></a>Создание пакета приложения
В этой статье описывается, как упаковать приложение Service Fabric и подготовить его для развертывания.

## <a name="package-layout"></a>Макет пакета
Манифест приложения, манифесты служб и другие необходимые файлы пакетов должны быть организованы в определенный макет для развертывания в кластере структуры службы. Примеры манифестов в этой статье потребовали бы организации в следующую структуру каталогов.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Папки должны иметь имена, соответствующие значениям атрибута **Name** каждого соответствующего элемента. Например, если манифест служб содержал два пакета кода с именами **MyCodeA** и **MyCodeB**, то две папки с такими же именами будут содержать необходимые двоичные файлы для каждого пакета кода.

## <a name="use-setupentrypoint"></a>Использование SetupEntryPoint
Типичные сценарии использования **SetupEntryPoint** относятся к ситуации, когда необходимо запустить исполняемый файл перед запуском службы или выполнить операцию с повышенными привилегиями. Например:

* Настройка и инициализация переменных среды, необходимых исполняемому файлу службы. Это касается не только исполняемых файлов, написанных с использованием моделей программирования Service Fabric. Например, npm.exe нужны определенные переменные среды, настроенные для развертывания приложения node.js.
* Настройка контроля доступа посредством установки сертификатов безопасности.

Дополнительные сведения о настройке **SetupEntryPoint** см. в статье [Настройка политик безопасности для приложения](service-fabric-application-runas-security.md).  

## <a name="configure"></a>Настройка 
### <a name="build-a-package-by-using-visual-studio"></a>Создание пакета с помощью Visual Studio
При использовании Visual Studio 2015 для создания приложения вы можете использовать команду Package, чтобы автоматически создать пакет, который будет соответствовать вышеописанному макету.

Чтобы создать пакет, щелкните правой кнопкой проект приложения в обозревателе решений и выберите команду "Пакет", как показано ниже.

![Создание пакета приложения с помощью Visual Studio][vs-package-command]

После завершения создания пакета в окне **Вывод** отобразятся сведения о расположении пакета. Шаг создания пакета выполняется автоматически при развертывании или отладке вашего приложения в Visual Studio.

### <a name="build-a-package-by-command-line"></a>Создание пакета с помощью командной строки
Приложение можно также упаковать программным способом, используя `msbuild.exe`. Этот файл выполняется в Visual Studio, так что результат будет тем же.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Тестирование пакета
Структуру пакета можно проверить локально средствами PowerShell, используя команду [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Эта команда проверит манифест на наличие ошибок при анализе, а также все ссылки. Эта команда позволяет проверить только правильность структуры каталогов и файлов в пакете.
Она не проверяет содержимое пакетов кода или данных, а только наличие всех необходимых файлов.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Эта ошибка показывает, что файл *MySetup.bat* , на который ссылается манифест служб **SetupEntryPoint** , отсутствует в пакете кода. После добавления нужного файла будет выполнена проверка приложения.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Если для приложения определены [параметры приложения](service-fabric-manage-multiple-environment-app-configuration.md), их можно передать в командлет [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) для полной проверки.

Если вы уже знаете, в какой кластер будет развернуто приложение, мы рекомендуем передать строку подключения к хранилищу образа. В этом случае для пакета будет выполнена проверка на совместимость с предыдущими версиями приложения, уже запущенными в кластере. Например, такая проверка позволит обнаружить, если пакет имеет одинаковую версию, но разное содержимое с развернутым ранее пакетом.  

Когда для приложения будет создан правильный пакет и успешно проведена проверка, оцените необходимость сжатия пакета, исходя из размера и числа файлов. 

## <a name="compress-a-package"></a>Сжатие пакета
Если пакет содержит большое число файлов или имеет большой размер, можно выполнить сжатие пакета для более быстрого развертывания. Сжатие уменьшает число файлов и размер пакета.
[Отправка пакета приложения](service-fabric-deploy-remove-applications.md#upload-the-application-package) может занять больше времени, чем отправка несжатого пакета, но зато [регистрация](service-fabric-deploy-remove-applications.md#register-the-application-package) и [отмена регистрации типа приложения](service-fabric-deploy-remove-applications.md#unregister-an-application-type) выполняется быстрее.

Для сжатых и несжатых пакетов используется одинаковый механизм развертывания. Если пакет сжат, он хранится в хранилище образов кластера именно в таком виде, а перед запуском приложения распаковывается на целевом узле.
Сжатие заменяет допустимый пакет Service Fabric его сжатой версией. Для этого нужны права на запись в папку. Если выполнить сжатие для уже сжатого пакета, никаких изменений не происходит. 

Чтобы сжать пакет, выполните команду Powershell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) с параметром `CompressPackage`. Чтобы распаковать сжатый пакет, выполните эту же команду с параметром `UncompressPackage`.

Следующая команда сжимает пакет, не копируя его в хранилище образов. Сжатый пакет можно скопировать в один или несколько кластеров Service Fabric, выполняя по мере необходимости командлет [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) без флага `SkipCopy`. Теперь пакет содержит ZIP-файлы для пакетов `code`, `config` и `data`. Манифест приложения и манифесты служб не сжимаются, так как они используются для разных внутренних операций (совместный доступ к пакетам, извлечение имени типа или версии приложения для некоторых проверок и т. д.).
Сжатие манифеста снизит эффективность таких операций.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Кроме того, сжатие и копирование пакета можно выполнить одним действием, используя командлет [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps).
Если пакет имеет большой размер, обеспечьте достаточное время ожидания для сжатия пакета и его отправки в кластер.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Для внутренней проверки Service Fabric вычисляет контрольные суммы для пакетов приложений. Если используется сжатие, контрольные суммы вычисляются для ZIP-версий каждого пакета.
Если вы скопировали несжатую версию пакета приложения и после этого решили применить для этого пакета сжатие, необходимо изменить версии пакетов `code`, `config` и `data`, чтобы избежать несовпадения контрольных сумм. Если пакеты не изменились, вместо изменения версии можно использовать [подготовку diff](service-fabric-application-upgrade-advanced.md). В этом случае не включайте пакет без изменений, просто добавьте ссылку на него из манифеста службы.

Аналогичным образом, если вы передали сжатую версию пакета и хотите использовать несжатый пакет, версии следует обновить, чтобы избежать несовпадения контрольных сумм.

Теперь пакет правильно упакован, проверен и сжат (если нужно), так что все готово для [развертывания](service-fabric-deploy-remove-applications.md) в один или несколько кластеров Service Fabric.

## <a name="next-steps"></a>Дальнейшие действия
В статье [Развертывание и удаление приложений с помощью PowerShell][10] представлены сведения об управлении экземплярами приложений с помощью PowerShell.

В статье [Управление параметрами приложения для нескольких сред][11] описано, как настроить параметры и переменные среды для различных экземпляров приложений.

В статье [Настройка политик безопасности для приложения][12] содержатся данные о работе служб в соответствии с политиками безопасности, ограничивающими доступ.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

