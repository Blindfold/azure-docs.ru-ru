---
title: "Изменение режима устройства StorSimple | Документация Майкрософт"
description: "В статье описываются режимы устройства StorSimple и объясняется, как изменить режим устройства StorSimple с помощью Windows PowerShell."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33c65bf2eecff3914f3227c76f7d638a4507e1f6


---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Переключение режима устройства StorSimple
В этой статье представлено краткое описание различных режимов, в которых могут работать устройства StorSimple. Устройство StorSimple может работать в трех режимах: нормальном, обслуживания и восстановления. 

После прочтения этой статьи вы узнаете:

* о режимах устройств StorSimple;
* о том, как выяснить, в каком режиме находится устройство StorSimple;
* о том, как переключиться из нормального режима в режим обслуживания и *наоборот*

Эти задачи можно выполнить только через интерфейс Windows PowerShell устройства StorSimple.

## <a name="about-storsimple-device-modes"></a>Режимы устройств StorSimple
Устройство StorSimple может работать в нормальном режиме, режиме обслуживания и режиме восстановления. Ниже кратко описан каждый из этих режимов.

### <a name="normal-mode"></a>Нормальный режим
Это нормальный рабочий режим полностью настроенного устройства StorSimple. По умолчанию устройство должно находиться в нормальном режиме.

### <a name="maintenance-mode"></a>Режим обслуживания
Иногда может потребоваться перевести устройство StorSimple в режим обслуживания. Этот режим позволяет выполнять обслуживание устройства и устанавливать критические обновления, например обновления встроенного ПО дисков.

Перевести систему в режим обслуживания можно только через Windows PowerShell для StorSimple. В этом режиме приостанавливаются все запросы ввода-вывода. Также останавливаются некоторые другие службы, например энергонезависимое ОЗУ (NVRAM) или служба кластеров. При входе в этот режим и выходе из него перезапускаются оба контроллера. При выходе из режима обслуживания все службы возобновляют работу и должны работать без ошибок. Это может занять несколько минут.

> [!NOTE]
> **Режим обслуживания поддерживается только на устройстве, которое работает корректно. Он не поддерживается на устройстве, на котором не работают один или оба контроллера.**
> </br>
> 
> 

### <a name="recovery-mode"></a>Режим восстановления
Режим восстановления можно описать как "безопасный режим Windows с поддержкой сети". Режим восстановления включает взаимодействие со службой поддержки Майкрософт, сотрудники которой выполняют диагностику системы. Основная цель режима восстановления — получение системных журналов.

Если система переходит в режим восстановления, вы должны обратиться в службу технической поддержки Майкрософт за дальнейшими действиями. Для получения дополнительных сведений [обратитесь в службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Переключить устройство в режим восстановления нельзя. Если устройство находится в неисправном состоянии, то в режиме восстановления будет предпринята попытка переключить его в то состояние, в котором его смогут изучить специалисты технической поддержки Майкрософт.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Определение режима устройства StorSimple
#### <a name="to-determine-the-current-device-mode"></a>Порядок определения текущего режима устройства
1. Войдите в последовательную консоль устройства, выполнив действия, описанные в разделе [Использование PuTTY для подключения к последовательной консоли устройства](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Просмотрите заглавное сообщение в меню последовательной консоли устройства. Это сообщение явно указывает, в каком режиме находится устройство: обслуживания или восстановления. Если сообщение не содержит никакой специальной информации, относящейся к режиму работы системы, то устройство находится в нормальном режиме.

## <a name="change-the-storsimple-device-mode"></a>Изменение режима устройства StorSimple
Устройство можно переключить в режим обслуживания (из нормального режима) для проведения обслуживания или установки обновлений режима обслуживания. Для ввода в режим обслуживания или выхода из него выполните следующие действия.

> [!IMPORTANT]
> Перед переключением в режим обслуживания убедитесь, что оба контроллера устройства работоспособны, проверив **состояние оборудования** на странице **Обслуживание** классического портала Azure. Если один или оба контроллера неработоспособны, обратитесь в службу поддержки Майкрософт для получения дальнейших указаний. Для получения дополнительных сведений [обратитесь в службу поддержки Майкрософт](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Переход в режим обслуживания
1. Войдите в последовательную консоль устройства, выполнив действия, описанные в разделе [Использование PuTTY для подключения к последовательной консоли устройства](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. В меню последовательной консоли выберите вариант 1 **Войти с полным доступом**. При выводе запроса введите **пароль администратора устройства**. Пароль по умолчанию: `Password1`.
3. В командной строке выполните следующую команду: 
   
    `Enter-HcsMaintenanceMode`
4. Отобразится предупреждение о том, что режим обслуживания будет мешать работе всех запросов ввода-вывода и приведет к разрыву подключения к классическом порталу Azure, в результате чего появится запрос на подтверждение. Нажмите клавишу **Y** , чтобы перейти в режим обслуживания.
5. Оба контроллера будут перезапущены. После завершения перезапуска отобразится сообщение о том, что устройство находится в режиме обслуживания.

#### <a name="to-exit-maintenance-mode"></a>Выход из режима обслуживания
1. Войдите в последовательную консоль устройства. Проверьте, находится ли устройство в режиме обслуживания, с помощью заглавного сообщения.
2. В командной строке выполните следующую команду:
   
    `Exit-HcsMaintenanceMode`
3. Отобразится сообщение с предупреждением и сообщение с запросом на подтверждение. Нажмите клавишу **Y** , чтобы выйти из режима обслуживания.
4. Оба контроллера будут перезапущены. После завершения перезапуска появится сообщение о том, что устройство находится в обычном режиме.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте, как [применить обновления нормального режима и режима обслуживания](storsimple-update-device.md) к своему устройству StorSimple.




<!--HONumber=Nov16_HO3-->


