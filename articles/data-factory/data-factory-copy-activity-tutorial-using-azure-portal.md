---
title: "Руководство. Создание конвейера с действием копирования с помощью портала Azure | Документация Майкрософт"
description: "В этом руководстве вы создадите конвейер фабрики данных Azure с действием копирования при помощи редактора фабрики данных на портале Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 079cb3e69954a9b02e26e005ad4bb1b7ef14c909
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-portal"></a>Руководство. Создание конвейера с действием копирования с помощью портала Azure
> [!div class="op_single_selector"]
> * [Обзор и предварительные требования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Мастер копирования](data-factory-copy-data-wizard-tutorial.md)
> * [Портал Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Шаблон Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [ИНТЕРФЕЙС REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

В этом руководстве рассматривается создание и мониторинг фабрики данных Azure с помощью портала Azure. Конвейер в фабрике данных копирует данные из хранилища BLOB-объектов Azure в базу данных SQL Azure с помощью действия копирования.

> [!NOTE]
> В этом руководстве конвейер данных копирует данные из исходного хранилища данных в целевое. Он не преобразовывает входные данные в выходные. Инструкции по преобразованию данных с помощью фабрики данных Azure см. в [руководстве по созданию конвейера для преобразования данных с помощью кластера Hadoop](data-factory-build-your-first-pipeline.md).
> 
> Можно объединить в цепочку два действия (выполнить одно действие вслед за другим), настроив выходной набор данных одного действия как входной набор данных другого действия. Подробные сведения см. в статье [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md). 

Ниже приведены шаги, которые вы выполните в процессе работы с этим руководством.

| Шаг | Описание |
| --- | --- |
| [Создание фабрики данных Azure](#create-data-factory) |На этом шаге вы создадите фабрику данных Azure с именем **ADFTutorialDataFactory**. |
| [Создание связанных служб](#create-linked-services) |На этом шаге создаются две связанные службы: **AzureStorageLinkedService** и **AzureSqlLinkedService**. <br/><br/>AzureStorageLinkedService связывает службу хранилища Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с ADFTutorialDataFactory. Входные данные для конвейера размещены в контейнере в хранилище BLOB-объектов Azure, а выходные данные будут сохраняться в таблице в базе данных SQL Azure. Таким образом, можно добавить эти два хранилища данных как связанные службы в фабрику данных. |
| [Создать входные и выходные наборы данных](#create-datasets) |На предыдущем шаге были созданы связанные службы, ссылающиеся на хранилища данных, содержащие входные и выходные данные. На этом этапе определяются два набора данных (**InputDataset** и **OutputDataset**), которые представляют входные и выходные данные, размещаемые в хранилищах данных. <br/><br/>Для InputDataset вам нужно указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными, а для OutputDataset — таблицу SQL для хранения выходных данных. Нужно указать и другие свойства, такие как структура, доступность данных и политика. |
| [Создать конвейер](#create-pipeline) |На этом шаге создается конвейер с именем **ADFTutorialPipeline** в ADFTutorialDataFactory. <br/><br/>Вам нужно добавить в конвейер **действие копирования**, которое копирует входные данные большого двоичного объекта Azure в выходную таблицу SQL Azure. Действие копирования перемещает данные в фабрике данных Azure. Это действие выполняется с помощью глобально доступной службы, обеспечивающей безопасное, надежное и масштабируемое копирование данных между разными хранилищами. Дополнительные сведения о действии копирования см. в статье [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md). |
| [Отслеживание конвейера](#monitor-pipeline) |На этом шаге отслеживаются срезы входных и выходных таблиц с помощью портала Azure. |

## <a name="prerequisites"></a>Предварительные требования
Прежде чем начать работу с этим руководством, выполните необходимые действия, перечисленные в [этой обзорной статье](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

## <a name="create-data-factory"></a>Создание фабрики данных
На этом шаге с помощью портала Azure создается фабрика данных с именем **ADFTutorialDataFactory**.

1. Войдите на [портал Azure](https://portal.azure.com/), нажмите кнопку **Создать**, откройте раздел **аналитики** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. В колонке **Создать фабрику данных** выполните следующие действия.
   
   1. Введите **ADFTutorialDataFactory** в поле **Имя**. 
      
         ![Создать колонку "Фабрика данных"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Имя фабрики данных Azure должно быть **глобально уникальным**. При возникновении указанной ниже ошибки измените имя фабрики данных (например, на ваше_имя_ADFTutorialDataFactory) и попробуйте создать фабрику данных снова. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](data-factory-naming-rules.md) , чтобы узнать о правилах именования артефактов фабрики данных.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Имя фабрики данных недоступно](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Выберите свою **подписку Azure**.
   3. Для группы ресурсов выполните одно из следующих действий.
      
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
          Некоторые действия, описанные в этом руководстве, предполагают, что для группы ресурсов используется имя **ADFTutorialResourceGroup**. Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
   4. Укажите **расположение** фабрики данных. В раскрывающемся списке отображаются только те регионы, которые поддерживаются службой фабрики данных.
   5. Выберите **Закрепить на начальной панели**.     
   6. Щелкните **Создать**.
      
      > [!IMPORTANT]
      > Создавать экземпляры фабрики данных может пользователь с ролью [Участник фабрики данных](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) на уровне подписки или группы ресурсов.
      > 
      > В будущем имя фабрики данных может быть зарегистрировано в качестве DNS-имени и, следовательно, стать отображаемым.                
      > 
      > 
3. Чтобы просмотреть сообщения о состоянии или сообщения уведомления, щелкните значок колокольчика на панели инструментов. 
   
   ![Сообщения уведомлений](./media/data-factory-copy-activity-tutorial-using-azure-portal/Notifications.png) 
4. Когда экземпляр будет создан, вы увидите колонку **Фабрика данных** , как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Создание связанных служб
Связанные службы связывают хранилища данных или службы вычислений с фабрикой данных Azure. См. список [поддерживаемых хранилищ данных](data-factory-data-movement-activities.md#supported-data-stores-and-formats) для всех источников и приемников, которые поддерживаются действием копирования. См. список [связанных служб вычислений](data-factory-compute-linked-services.md), поддерживаемых фабрикой данных. В этом руководстве не рассматривается использование служб вычислений. 

На этом шаге создаются две связанные службы: **AzureStorageLinkedService** и **AzureSqlLinkedService**. Связанная служба AzureStorageLinkedService связывает учетную запись хранения Azure, а AzureSqlLinkedService связывает базу данных SQL Azure с **ADFTutorialDataFactory**. Далее в этом руководстве будет создан конвейер, который копирует данные из контейнера больших двоичных объектов в AzureStorageLinkedService в таблицу SQL в AzureSqlLinkedService.

### <a name="create-a-linked-service-for-the-azure-storage-account"></a>Создание связанной службы для учетной записи хранилища Azure
1. В колонке **Фабрика данных** щелкните плитку **Создать и развернуть**, чтобы запустить **редактор** для фабрики данных.
   
   ![Плитка "Создание и развертывание"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. В **редакторе** щелкните **Новое хранилище данных** на панели инструментов и выберите в раскрывающемся меню пункт **Служба хранилища Azure**. На панели справа должен появиться шаблон JSON для создания связанной службы хранилища Azure. 
   
    ![Кнопка "Создать хранилище данных" в редакторе](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Замените `<accountname>` и `<accountkey>` значениями имени и ключа учетной записи хранения Azure. 
   
    ![Хранилище больших двоичных объектов JSON в редакторе](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. На панели инструментов щелкните **Развернуть** . Теперь в иерархическом представлении должен отобразиться развернутый экземпляр **AzureStorageLinkedService** . 
   
    ![Развертывание хранилища больших двоичных объектов в редакторе](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

> [!NOTE]
> Дополнительные сведения о свойствах файлов JSON см. в статье [Перемещение данных в большой двоичный объект Azure и из него с помощью фабрики данных Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).
> 
> 

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Создание связанной службы для базы данных SQL Azure.
1. В **редакторе фабрики данных** на панели инструментов щелкните **Новое хранилище данных** и выберите в раскрывающемся меню пункт **База данных SQL Azure**. В правой панели должен появиться шаблон JSON для создания связанной службы SQL Azure.
2. Замените `<servername>`, `<databasename>`, `<username>@<servername>` и `<password>` именем своего сервера SQL Azure, именем учетной записи пользователя и паролем. 
3. На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть экземпляр **AzureSqlLinkedService**.
4. Экземпляр **AzureSqlLinkedService** должен отображаться в иерархическом представлении. 

> [!NOTE]
> Дополнительные сведения о свойствах файлов JSON см. в статье [Перемещение данных в базу данных SQL Azure и из нее с помощью фабрики данных Azure](data-factory-azure-sql-connector.md#linked-service-properties).
> 
> 

## <a name="create-datasets"></a>Создание наборов данных
На предыдущем шаге были созданы связанные службы **AzureStorageLinkedService** и **AzureSqlLinkedService** для связи учетной записи хранения Azure и базы данных SQL Azure с фабрикой данных **ADFTutorialDataFactory**. На этом шаге будут определены два набора данных, **InputDataset** и **OutputDataset**, представляющие входные и выходные данные в хранилищах данных, на которые ссылаются службы AzureStorageLinkedService и AzureSqlLinkedService соответственно. Для InputDataset вам нужно указать контейнер больших двоичных объектов, содержащий большой двоичный объект с исходными данными, а для OutputDataset — таблицу SQL для хранения выходных данных. 

### <a name="create-input-dataset"></a>Создание входного набора данных
На этом шаге создается набор данных с именем **InputDataset**. Он указывает на контейнер больших двоичных объектов в службе хранилища Azure, которая представлена связанной службой **AzureStorageLinkedService**.

1. В **редакторе** фабрики данных щелкните **... Дополнительно**, **Новый набор данных**, а затем в раскрывающемся меню выберите пункт **Хранилище BLOB-объектов**. 
   
    ![Меню "Новый набор данных"](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Замените JSON на правой панели следующим фрагментом JSON: 
   
    ```JSON
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   
    Обратите внимание на следующие моменты. 
   
    - Для параметра **type** набора данных задано значение **AzureBlob**.
    - Для **linkedServiceName** задано значение **AzureStorageLinkedService**. Эта связанная служба была создана на шаге 2.
    - В качестве значения **folderPath** установлен контейнер **adftutorial**. Вы также можете указать имя большого двоичного объекта в папке, используя свойство **fileName** . Так как вы не указываете имя большого двоичного объекта, данные из всех больших двоичных объектов в контейнере считаются входными данными.
    - Для **type** формата установлено значение **TextFormat**.
    - В этом текстовом файле содержатся два поля, **FirstName** и **LastName**, которые разделяются запятой (**columnDelimiter**).
    - Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**). Следовательно, служба фабрики данных будет искать входные данные в корневом каталоге указанного вами контейнера BLOB-объектов (**adftutorial**) каждый час. 
     
     Если параметр **fileName** для **входного** набора данных не задан, все файлы и большие двоичные объекты из входной папки (**folderPath**) считаются входными данными. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные.
     
     Если не указать **fileName** для **выходной таблицы**, то созданные в **folderPath** файлы получают имена в следующем формате: Data.&lt;Guid&gt;.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
     
     Чтобы динамически установить параметры **folderPath** и **fileName** на основе времени **SliceStart**, используйте свойство **partitionedBy**. В следующем примере folderPath использует год, месяц и день из SliceStart (время начала обработки среза), а в fileName используется время (часы) из SliceStart. Например, если срез создается для метки времени 2016-09-20T08:00:00, свойству folderName присваивается значение wikidatagateway/wikisampledataout/2016/09/20, а свойству fileName — значение 08.csv. 

    ```JSON     
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
       { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
       { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
       { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
       { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],
    ```
3. На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть набор данных **InputDataset**. Набор данных **InputDataset** должен отображаться в иерархическом представлении.

> [!NOTE]
> Дополнительные сведения о свойствах файлов JSON см. в статье [Перемещение данных в большой двоичный объект Azure и из него с помощью фабрики данных Azure](data-factory-azure-blob-connector.md#dataset-properties).
> 
> 

### <a name="create-output-dataset"></a>Создание выходного набора данных
На этом этапе мы создадим выходной набор данных с именем **OutputDataset**. Этот набор данных указывает на таблицу SQL в базе данных SQL Azure (представлена значением **AzureSqlLinkedService**). 

1. В **редакторе** фабрики данных щелкните **... Дополнительно**, **Новый набор данных**, а затем в раскрывающемся меню выберите пункт **Azure SQL**. 
2. Замените JSON на правой панели следующим фрагментом JSON:

    ```JSON   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```       
    Обратите внимание на следующие моменты. 
   
    - Для параметра **type** набора данных задано значение **AzureSQLTable**.
    - **linkedServiceName** имеет значение **AzureSqlLinkedService** (эта связанная служба была создана на шаге 2).
    - **tablename** имеет значение **emp**.
    - В таблице emp в базе данных есть три столбца: **ID**, **FirstName** и **LastName**. ID — это столбец для идентификаторов, поэтому здесь вам нужно указать только значения **FirstName** и **LastName**.
    - Параметр **availability** имеет значение **hourly** (параметру **frequency** присваивается значение **hour**, а параметру **interval** — значение **1**).  Служба фабрики данных каждый час создает срез выходных данных в таблице **emp** в базе данных SQL Azure.
3. На панели инструментов щелкните **Развернуть**, чтобы создать и развернуть набор данных **OutputDatase**. Набор данных **OutputDataset** должен отображаться в иерархическом представлении. 

> [!NOTE]
> Дополнительные сведения о свойствах файлов JSON см. в статье [Перемещение данных в базу данных SQL Azure и из нее с помощью фабрики данных Azure](data-factory-azure-sql-connector.md#linked-service-properties).
> 
> 

## <a name="create-pipeline"></a>Создание конвейера
На этом этапе создается конвейер с **действием копирования**, которое использует **InputDataset** в качестве входных данных и **OutputDataset** в качестве выходных.

1. В **редакторе** фабрики данных щелкните **... Дополнительно** и **Новый конвейер**. Кроме того, можно щелкнуть правой кнопкой мыши **Конвейеры** в древовидном представлении и выбрать **Создать конвейер**.
2. Замените JSON на правой панели следующим фрагментом JSON: 

    ```JSON   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 
    ```   
    
    Обратите внимание на следующие моменты.
   
    - В разделе действий доступно только одно действие, параметр **type** которого имеет значение **Copy**.
    - Для этого действия параметру input присвоено значение **InputDataset**, а параметру output — значение **OutputDataset**.
    - В разделе **typeProperties** в качестве типа источника указано **BlobSource**, а в качестве типа приемника — **SqlSink**.
     
    Замените значение свойства **start** текущей датой, а значение свойства **end** — датой следующего дня. Можно указать только часть даты и пропустить временную часть указанной даты и времени. Например, 2016-02-03, что эквивалентно 2016-02-03T00:00:00Z.
     
    Даты начала и окончания должны быть в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2016-10-14T16:32:41Z. Время **окончания** указывать не обязательно, однако в этом примере мы будем его использовать. 
     
    Если не указать значение свойства **end**, оно вычисляется по формуле "**время начала + 48 часов**". Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9999-09-09** в качестве значения свойства **end**.
     
    В примере выше получено 24 среза данных, так как они создаются каждый час.
3. Щелкните **Развернуть** на панели инструментов, чтобы создать и развернуть конвейер **ADFTutorialPipeline**. Убедитесь, что конвейер отображается в иерархической структуре. 
4. Теперь закройте колонку **Редактор**, щелкнув **X**. Щелкните **X** снова, чтобы отобразить домашнюю страницу **фабрики данных** для экземпляра **ADFTutorialDataFactory**.

**Поздравляем!** Вы успешно создали фабрику данных Azure, связанные службы, таблицы и конвейер, а также выполнили планирование конвейера.   

### <a name="view-the-data-factory-in-a-diagram-view"></a>Просмотр фабрики данных в представлении схемы
1. В колонке **Фабрика данных** щелкните **Схема**.
   
    ![Колонка "Фабрика данных" — плитка схемы](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Вы должны увидеть схему, аналогичную приведенной ниже: 
   
    ![Представление схемы](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)
   
    Можно увеличивать и уменьшать масштаб, выбирать 100%-й масштаб или масштаб по размеру, автоматически размещать конвейеры и таблицы, а также отображать сведения из журнала обращений и преобразований (выделение восходящих и нисходящих элементов для выбранных элементов).  Дважды щелкните объект (входную или выходную таблицу либо конвейер), чтобы просмотреть его свойства. 
3. В представлении схемы щелкните правой кнопкой мыши и **откройте конвейер** **ADFTutorialPipeline**. 
   
    ![Открыть конвейер](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)
4. Будут отображены действия в конвейере вместе с входными и выходными наборами данных для этих действий. В этом руководстве в конвейере используется только одно действие (действие копирования) с объектом InputDataset в качестве входного набора данных и OutputDataset в качестве выходного набора данных.   
   
    ![Открытое представление конвейера](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenedPipeline.png)
5. Щелкните **Фабрика данных** в строке навигации в верхнем левом углу, чтобы перейти к представлению схемы. В представлении схемы отображаются все конвейеры. В этом примере создан только один конвейер.   

## <a name="monitor-pipeline"></a>Отслеживание конвейера
На этом шаге используется портал Azure для мониторинга фабрики данных Azure. 

### <a name="monitor-pipeline-using-diagram-view"></a>Мониторинг конвейера с использованием представления схемы
1. Щелкните **X**, чтобы закрыть представление **схемы** и перейти на домашнюю страницу фабрики данных. Если вы закрыли браузер, сделайте следующее. 
   1. Перейдите на [портал Azure](https://portal.azure.com/). 
   2. Дважды щелкните **ADFTutorialDataFactory** на **начальной панели** или щелкните **Фабрика данных** в меню слева и выполните поиск по запросу "ADFTutorialDataFactory". 
2. Вы увидите количество и имена таблиц, а также конвейер, который вы создали в этой колонке.
   
    ![домашняя страница с именами](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png)
3. Теперь щелкните плитку **Наборы данных** .
4. В колонке **Наборы данных** щелкните **InputDataset**. Это входной набор данных для **ADFTutorialPipeline**.
   
    ![Наборы данных с выбранным элементом InputDataset](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Щелкните **… (многоточие)** , чтобы отобразить все срезы данных.
   
    ![Все срезы входных данных](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Обратите внимание, что срезы данных сейчас находятся в состоянии **Готово**, так как файл **emp.txt** все это время существует в контейнере больших двоичных объектов **adftutorial\input**. Убедитесь, что срезы не отображаются в разделе **Срезы, в которых недавно произошел сбой** в нижней части.
   
    Оба списка, **Последние обновленные срезы** и **Последние срезы, завершившиеся сбоем**, сортируются по **последнему времени обновления**. 
   
    Чтобы отфильтровать срезы, выберите пункт **Фильтр** на панели инструментов.  
   
    ![Фильтрация входных срезов](./media/data-factory-copy-activity-tutorial-using-azure-portal/filter-input-slices.png)
6. Закрывайте колонки, пока не увидите колонку **Наборы данных** . Щелкните **OutputDataset**. Это выходной набор данных для **ADFTutorialPipeline**.
   
    ![Колонка "Наборы данных"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png)
7. Колонка **OutputDataset** должна выглядеть так:
   
    ![Колонка "Таблица"](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png) 
8. Обратите внимание, что срезы данных до текущего момента времени уже выполнены и все они находятся в состоянии **Готов**. В разделе **Проблемные срезы** в нижней части окна срезов нет.
9. Нажмите кнопку **… (многоточие)** , чтобы отобразить все срезы.
   
    ![Колонка срезов данных](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png)
10. Щелкните любой срез данных в списке, чтобы отобразить колонку **Срез данных** .
    
     ![Колонка среза данных](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Если срез не находится в состоянии **Готов**, вы можете увидеть восходящие срезы, которые не находятся в состоянии готовности и блокируют выполнение текущего среза в списке **Неготовые восходящие срезы**.
11. В колонке **СРЕЗ ДАННЫХ** в списке в нижней части окна отображаются все выполненные действия. Щелкните **выполняемое действие**, чтобы просмотреть колонку **Подробности о выполнении операции**. 
    
    ![Сведения о выполнении действия](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)
12. С помощью кнопки **X** закройте все колонки, чтобы вернуться к начальной колонке **ADFTutorialDataFactory**.
13. (Необязательно.) Щелкните **Конвейеры** на домашней странице фабрики данных **ADFTutorialDataFactory**, щелкните **ADFTutorialPipeline** в колонке **Конвейеры** и изучите входные таблицы (**Использованные**) или выходные таблицы (**Созданные**).
14. Запустите **SQL Server Management Studio**, подключитесь к базе данных SQL Azure и убедитесь, что строки вставляются в таблицу **emp** в базе данных.
    
    ![результаты SQL-запроса](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Мониторинг конвейера с использованием приложения по мониторингу и управлению
Для мониторинга конвейеров также можно использовать приложение по мониторингу и управлению. Дополнительные сведения об использовании этого приложения см. в статье [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md).

1. Щелкните плитку **Monitor & Manage** (Мониторинг и управление) на домашней странице фабрики данных.
   
    ![Плитка Monitor & Manage (Мониторинг и управление)](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Вы должны увидеть **приложение по мониторингу и управлению**. Измените значения параметров **Время начала** и **Время окончания**, чтобы они включали соответствующие значения (2016-07-12 и 2016-07-13) для конвейера, и щелкните **Применить**. 
   
    ![Приложение по мониторингу и управлению](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png) 
3. Выберите окно действия в списке **Activity Windows** (Окна действий), чтобы просмотреть сведения о нем. 
    ![Сведения об окне действия](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

## <a name="summary"></a>Сводка
В этом учебнике вы создали фабрику данных Azure для копирования данных из большого двоичного объекта Azure в базу данных SQL Azure. Вы использовали портал Azure для создания фабрики данных, связанных служб, наборов данных и конвейера. Вот обобщенные действия, которые вы выполнили в этом руководстве:  

1. Создание **фабрики данных Azure**.
2. Создание **связанных служб**.
   1. **Служба хранилища Azure** — связанная служба для связи с учетной записью хранения Azure, которая содержит входные данные.     
   2. **SQL Azure** — связанная служба для связи с базой данных SQL Azure, которая содержит выходные данные. 
3. Создание **наборов данных** , которые описывают входные и выходные данные для конвейеров.
4. Создание **конвейера** с **BlobSource** в качестве источника и **SqlSink** в качестве приемника с помощью **действия копирования**.  

## <a name="see-also"></a>См. также
| Раздел | Описание |
|:--- |:--- |
| [Конвейеры](data-factory-create-pipelines.md) |Эта статья содержит сведения о конвейерах и действиях в фабрике данных Azure. |
| [Наборы данных](data-factory-create-datasets.md) |Эта статья поможет вам понять, что такое наборы данных в фабрике данных Azure. |
| [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) |Здесь объясняются аспекты планирования и исполнения в модели приложений фабрики данных. |

