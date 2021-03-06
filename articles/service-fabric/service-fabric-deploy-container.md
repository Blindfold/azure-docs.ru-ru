---
title: "Service Fabric и развертывание контейнеров | Документация Майкрософт"
description: "Service Fabric и использование контейнеров для развертывания приложений микрослужб. В этой статье рассмотрены возможности, которые платформа Service Fabric предоставляет для контейнеров. Также здесь описано развертывание образа контейнера Windows в кластере."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/17/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 97b0cb7a5f04f2c5c547cb4b70d87273aa8f2383
ms.lasthandoff: 02/21/2017


---
# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Предварительная версия: развертывание контейнера Windows в Service Fabric
> [!div class="op_single_selector"]
> * [Развертывание контейнера Windows](service-fabric-deploy-container.md)
> * [Развертывание контейнера Docker](service-fabric-deploy-container-linux.md)
> 
> 

В этой статье подробно рассматривается создание контейнерных служб в контейнерах Windows.

> [!NOTE]
> Эта функция доступна в режиме предварительной версии для Windows Server 2016.
>  

В Service Fabric реализовано несколько способов использования контейнеров для создания приложений, состоящих из контейнерных микрослужб. 

Среди этих способов:

* развертывание и активация образа контейнера;
* управление ресурсами;
* проверка подлинности репозитория;
* сопоставление порта контейнера с портом узла;
* межконтейнерное обнаружение и взаимодействие;
* возможность настройки и установки переменных среды.

Давайте рассмотрим все эти возможности на примере упаковки контейнерной службы, которая будет включена в приложение.

## <a name="package-a-windows-container"></a>Упаковка контейнера Windows
При упаковке контейнера вы можете использовать шаблон проекта Visual Studio или [создать пакет приложения вручную](#manually).  Если вы используете Visual Studio, шаблон проекта создаст для вас структуру пакета приложения и файлы манифеста.

> [!TIP]
> Упаковать имеющийся образ контейнера в службу проще всего с помощью Visual Studio.

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Упаковка существующего образа контейнера с помощью Visual Studio
Visual Studio предоставляет шаблон службы Service Fabric для развертывания контейнера файла в кластере Service Fabric.

1. Чтобы создать приложение Service Fabric, выберите **Файл** > **Создать проект**.
2. Выберите **гостевой контейнер** в качестве шаблона службы.
3. Выберите **имя образа** и укажите путь к образу в репозитории контейнера (например, https://hub.docker.com), например myrepo/myimage:v1. 
4. Присвойте службе имя и нажмите кнопку **ОК**.
5. Если контейнерной службе нужна конечная точка для обмена данными, можно добавить значения протокола, порта и типа в файл ServiceManifest.xml. Например: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    Если указать `UriScheme`, конечная точка контейнера будет автоматически зарегистрирована в службе именования Service Fabric, что улучшит ее поиск. Порт может быть фиксированным (как показано в приведенном выше примере) или выделяться динамически (поле остается пустым и порт выделяется из диапазона портов назначенного приложения), как и в случае с любой службой.
    Кроме того, необходимо настроить сопоставление порта контейнера с портом узла, указав политику `PortBinding` в манифесте приложения, как описано ниже.
6. Если для контейнера требуется управление ресурсами, добавьте `ResourceGovernancePolicy`.
8. Если для контейнера требуется аутентификация в частном репозитории, добавьте `RepositoryCredentials`.
7. Теперь можно использовать пакет и опубликовать действие в локальном кластере, если используется Windows Server 2016 с активированной поддержкой контейнеров. 
8. Когда все будет готово, можно опубликовать приложение на удаленном кластере или вернуть решение в систему управления версиями. 

В качестве примера приложения можно ознакомиться с [примерами кода контейнера Service Fabric на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="creating-a-windows-server-2016-cluster"></a>Создание кластера Windows Server 2016
Чтобы развернуть контейнерное приложение, необходимо создать кластер под управлением Windows Server 2016 с поддержкой контейнеров. Это можно сделать на локальном компьютере разработки или в Azure с помощью Azure Resource Manager (ARM). 

Чтобы развернуть кластер с помощью ARM, выберите параметр образа **Windows Server 2016 with Containers** (Windows Server 2016 с контейнерами) в Azure. Ознакомьтесь со статьей [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Обязательно используйте приведенные ниже параметры ARM.

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
Для создания кластера можно также использовать шаблон ARM кластера с&5; узлами, доступный [здесь](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype). Кроме того, можно прочитать [записи блога Leok](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html) об использовании Service Fabric и контейнеров Windows.

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Упаковка и развертывание образа контейнера вручную
Упаковка контейнерной службы вручную включает следующие этапы.

1. Публикация контейнеров в репозиторий.
2. Создание структуры каталогов пакета.
3. Редактирование файла манифеста службы.
4. Редактирование файла манифеста приложения.

## <a name="deploy-and-activate-a-container-image"></a>Развертывание и активация образа контейнера
В [модели приложения](service-fabric-application-model.md)Service Fabric контейнер представляет собой узел приложения, в котором размещается несколько реплик службы. Чтобы развернуть и активировать контейнер, вставьте имя образа контейнера в элемент `ContainerHost` в манифесте служб.

Добавьте в манифест службы элемент `ContainerHost` для точки входа. Затем установите `ImageName` в качестве имени репозитория и образа контейнера. Следующий фрагмент манифеста содержит пример развертывания контейнера с именем `myimage:v1` из репозитория с именем `myrepo`.

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

Входные команды можно передать с помощью необязательного элемента `Commands` в формате разделенного запятыми набора команд, которые нужно выполнить в самом контейнере.

## <a name="understand-resource-governance"></a>Концепция управления ресурсами
Управление ресурсами подразумевает, что вы можете определять ресурсы, которые контейнер может использовать на узле. Параметр `ResourceGovernancePolicy`, определяемый в манифесте приложения, позволяет объявить ограничения для ресурсов, доступных из пакета кода службы. Ограничения ресурсов можно задать для следующих ресурсов:

* Память
* MemorySwap;
* CpuShares;
* MemoryReservationInMB;  
* BlkioWeight.

> [!NOTE]
> В будущих выпусках будет добавлена поддержка ограничений на определенные параметры блочного ввода-вывода, включая число операций ввода-вывода, скорость чтения-записи и т. п.
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Проверка подлинности в репозитории
Для скачивания контейнера вам могут понадобится учетные данные для входа в репозиторий контейнера. Учетные данные для входа, указанные в манифесте приложения, определяют имя и пароль для входа (или ключ SSH). Они нужны для скачивания образа контейнера из репозитория образов. В следующем примере представлена учетная запись с именем *TestUser* и паролем в виде открытого текста (такая практика *не рекомендуется*).

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Мы советуем шифровать пароль с использованием сертификата, развернутого на компьютере.

В следующем примере представлена учетная запись с именем *TestUser* и паролем, зашифрованным с помощью сертификата с именем *MyCert*. Можно использовать команду PowerShell `Invoke-ServiceFabricEncryptText` для создания секретного текста зашифрованного пароля. Этот процесс описан в статье [Управление секретами в приложениях Service Fabric](service-fabric-application-secret-management.md).

На локальном компьютере следует развернуть закрытый ключ сертификата, который позволит расшифровывать на нем пароль, с использованием внешних средств. (В Azure для этого используется Azure Resource Manager.) Когда Service Fabric развернет на компьютере пакет службы, секретный код можно будет расшифровать, а затем выполнить проверку подлинности в репозитории контейнера, используя этот секрет и имя учетной записи.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>Настройка сопоставления порта контейнера с портом узла
С помощью политики `PortBinding` в манифесте приложения можно указать порт узла, который будет использоваться для обмена данными с контейнером. Это сопоставление связывает порт, который прослушивает служба в контейнере, с портом на узле.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Настройка межконтейнерного поиска и обмена данными
С помощью политики `PortBinding` в манифесте службы можно сопоставить порт контейнера с конечной точкой `Endpoint`, как показано в следующем примере. Конечная точка `Endpoint1` может определять фиксированный порт (например, порт 80). Если не указать здесь номер порта, будет автоматически выбран случайный порт из диапазона портов для приложений кластера.

Если указать конечную точку с помощью тега `Endpoint` в манифесте службы для гостевого контейнера, Service Fabric может автоматически опубликовать эту конечную точку в службе именования. Это позволит другим службам, которые выполняются в кластере, находить этот контейнер с помощью запросов REST.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Зарегистрировав контейнер в службе именования, вы легко обеспечите обмен данными между контейнерами в коде своего контейнера, используя [обратный прокси-сервер](service-fabric-reverseproxy.md). Для этого достаточно указать в переменных среды http-порт прослушивания обратного прокси-сервера и имена служб, с которыми будет выполняться обмен данными. Этот процесс описан в следующем разделе. 

## <a name="configure-and-set-environment-variables"></a>Настройка и установка переменных среды
Переменные среды можно указывать в манифесте служб отдельно для каждого пакета кода — как для служб, развернутых в контейнере, так и для служб, развернутых в качестве процессов или гостевых исполняемых файлов. Указанные значения переменных среды можно переопределить в манифесте приложения или указать как параметры приложения во время развертывания.

Следующий фрагмент XML-кода из манифеста службы демонстрирует, как определить переменные среды для пакета кода.

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

Эти переменные среды можно переопределить на уровне манифеста приложения:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

В приведенном выше примере мы указали явное значение (19000) для переменной среды `HttpGateway`. Значение для параметра `BackendServiceName` мы указали с помощью параметра приложения `[BackendSvc]`. Так мы можем указать значение для `BackendServiceName` при развертывании приложения, а не использовать фиксированное значение в манифесте.

## <a name="complete-examples-for-application-and-service-manifest"></a>Полные примеры манифестов для приложения и службы

Ниже приведен пример манифеста приложения.

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

А вот пример манифеста службы (указывается в предложенном выше манифесте приложения).

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы успешно развернули контейнерную службу, изучите рекомендации по управлению ее жизненным циклом в статье [Жизненный цикл приложения Service Fabric](service-fabric-application-lifecycle.md).

* [Общие сведения о Service Fabric и контейнерах](service-fabric-containers-overview.md)
* В качестве примера приложения можно ознакомиться с [примерами кода контейнера Service Fabric на сайте GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

