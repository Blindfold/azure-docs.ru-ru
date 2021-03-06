---
title: "Использование Pig в Hadoop в HDInsight с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как отправлять задания Pig в кластер Hadoop в HDInsight с помощью Azure PowerShell."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: dcc248d398f2e2aa0f563a0868e80b1e7ee6a3fb
ms.lasthandoff: 04/12/2017

---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Использование Azure PowerShell для выполнения заданий Pig в HDInsight

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования Azure PowerShell для отправки заданий Pig в Hadoop в кластере HDInsight. Pig позволяет написать задания MapReduce с использованием языка (Pig Latin), который моделирует преобразования данных, а не функции сопоставления и приведения.

> [!NOTE]
> В этом документе не приводится подробное описание процессов, которые выполняют операторы Pig Latin, используемые в примерах. Информацию об операторах Pig Latin, используемых в данном примере, см. в статье [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Предварительные требования

* **Кластер Azure HDInsight**.

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. См. дополнительные сведения о [нерекомендуемых версиях HDInsight в Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* **Рабочая станция с Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>Выполнение заданий Pig с помощью PowerShell

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания Pig в HDInsight. Во внутренних процессах PowerShell использует вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat), выполняющемуся на кластере HDInsight.

При выполнении заданий Pig на удаленном кластере HDInsight используются следующие командлеты:

* **Login-AzureRmAccount**— выполняет аутентификацию Azure PowerShell для подписки Azure.
* **New-AzureRmHDInsightPigJobDefinition** — создает *определение задания* с использованием заданных операторов Pig Latin.
* **Start-AzureRmHDInsightJob**— отправляет определение задания в HDInsight, запускает задание и возвращает объект- *задание* , который можно использовать для проверки состояния задания.
* **Wait-AzureRmHDInsightJob**— использует объект-задание для проверки состояния задания. Он ждет завершения задания или превышения времени ожидания.
* **Get-AzureRmHDInsightJobOutput**— используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight.

1. С помощью редактора сохраните следующий код как **pigjob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Откройте командную строку Windows PowerShell. Перейдите к расположению файла **pigjob.ps1** , а затем используйте следующую команду для запуска сценария:

        .\pigjob.ps1

    Вам будет предложено войти в свою подписку Azure. Затем вам будет предложено ввести данные HTTPS, имя и пароль учетной записи администратора для кластера HDInsight.

2. После завершения задания должна быть возвращена информация следующего вида.

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **pigjob.ps1** , сохраните его, а затем запустите снова.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Будет возвращена информация, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

## <a id="summary"></a>Сводка
Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания Pig в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

