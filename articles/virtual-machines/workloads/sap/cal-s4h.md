---
title: "Развертывание SAP S/4HANA или BW/4HANA на виртуальной машине Azure | Документация Майкрософт"
description: "Развертывание SAP S/4HANA или BW/4HANA на виртуальной машине Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2a645e6ab50b7b764a56bb7a79ad5cf295881214
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Развертывание SAP S/4HANA или BW/4HANA в Microsoft Azure
В этой статье мы расскажем, как развернуть платформу S/4HANA в Microsoft Azure с помощью SAP Cloud Appliance Library (SAP CAL) 3.0. Развертывание других решений на основе SAP HANA (например, BW/4 HANA) выполняется аналогичным образом. Нужно только выбрать другое решение.

> [!NOTE]
Дополнительные сведения о SAP CAL см. на [домашней странице сайта разработчиков этой платформы](https://cal.sap.com/). Перейти на страницу блога, посвященного SAP Cloud Appliance Library 3.0, вы можете [здесь](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

## <a name="step-by-step-process-to-deploy-the-solution"></a>Пошаговый процесс развертывания решения

На снимках экрана, представленных ниже, показано развертывание S/4HANA в Azure. Процесс развертывания других решений, например BW/4 HANA, аналогичен описанному ниже.

На первом снимке экрана отображаются все решения на основе SAP CAL HANA, доступные в Azure. Обратите внимание, что **локальный выпуск SAP S/4HANA** представлен в самом низу.

![Снимок экрана с окном решений SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic-1b.jpg)

Сначала вам необходимо создать учетную запись SAP CAL. На вкладке **Учетные записи** представлены два варианта выбора Azure: Microsoft Azure и Microsoft Azure operated by 21Vianet (Microsoft Azure под управлением 21Vianet). Для этого примера выберем **Microsoft Azure**.

![Снимок экрана с окном учетных записей SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic-2.jpg)

Далее введите идентификатор подписки Azure, который можно найти на портале Azure. Затем скачайте сертификат управления Azure.

![Снимок экрана с окном учетных записей SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Чтобы найти идентификатор подписки Azure, воспользуйтесь именно классическим порталом Azure, а не его новой версией. Так как продукт SAP CAL еще на адаптирован для новой версии, он все еще использует классический портал Azure для работы с сертификатами управления.

На снимке экрана, представленном ниже, отображается классический портал. В разделе **Параметры** выберите вкладку **Подписки**, чтобы найти идентификатор подписки, который нужно ввести в окне учетных записей SAP CAL.

![Снимок экрана с классическим порталом Azure](./media/cal-s4h/s4h-pic4b.jpg)

Выберите вкладку **Сертификаты управления** раздела **Параметры**. Отправьте сертификат управления для получения разрешения SAP CAL на создание виртуальных машин в рамках подписки клиента. (Отправляемый сертификат управления — это сертификат, предварительно скачанный с SAP CAL.)

![Снимок экрана с классическим порталом Azure. Вкладка "Сертификаты управления"](./media/cal-s4h/s4h-pic5.jpg)

Выберите скачанный файл сертификата во всплывающем диалоговом окне.

![Снимок экрана с диалоговым окном для отправки сертификата управления](./media/cal-s4h/s4h-pic8.jpg)

Отправив сертификат, в SAP CAL проверьте подключение между SAP CAL и подпиской Azure. Должно появиться окно с сообщением о допустимом подключении.

![Снимок экрана с окном учетных записей SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic9.jpg)

Далее выберите решение для развертывания и создайте экземпляр.
Введите имя экземпляра, выберите регион Azure и определите основной пароль для доступа к решению.

![Снимок экрана с окном решений SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic10.jpg)

В зависимости от размера и сложности решения (оценку предоставляет SAP CAL) через некоторое время состояние экземпляра сменится на "Активный", и он будет готов к использованию.

![Снимок экрана с окном экземпляров SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic11.jpg)

В примере ниже представлены некоторые дополнительные сведения о решении, например типы развернутых виртуальных машин. В данном случае созданы три виртуальные машины Azure разных размеров и для разных целей.

![Снимок экрана с окном экземпляров SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic12.jpg)

На классическом портале Azure эти виртуальные машины можно найти по тому же имени экземпляра, указанному в SAP CAL.

![Снимок экрана, на котором отображаются три виртуальные машины на классическом портале Azure](./media/cal-s4h/s4h-pic13.jpg)

Теперь вы можете подключиться к решению с помощью кнопки подключения на портале SAP CAL. В диалоговом окне содержится ссылка на руководство пользователя, в котором представлены все учетные данные по умолчанию, необходимые для работы с решением.

![Снимок экрана с диалоговым окном подключения к экземпляру](./media/cal-s4h/s4h-pic14b.jpg)

Другой вариант — войти на клиентскую виртуальную машину Windows и запустить, например, предварительно настроенный интерфейс SAP GUI.

![Снимок экрана с предварительно настроенным интерфейсом SAP GUI](./media/cal-s4h/s4h-pic15.jpg)

