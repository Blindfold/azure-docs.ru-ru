---
title: "Как администраторы управляют пользователями и устройствами с помощью Azure MFA | Документы Microsoft"
description: "Эта статья содержит информацию о том, как изменить параметры пользователя (например, чтобы от пользователей требовалось повторно проходить проверку)."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Управление параметрами пользователей в облаке с помощью Azure Multi-Factor Authentication
Как администратор вы можете управлять следующими параметрами пользователей и устройств.

* Требовать у выбранных пользователей сообщать о способах связи еще раз
* Удалять существующие пароли приложений пользователей
* Восстановить Multi-Factor Authentication на всех приостановленных устройствах для пользователя

## <a name="require-selected-users-to-provide-contact-methods-again"></a>Требовать у выбранных пользователей сообщать о способах связи еще раз
Если этот параметр включен, пользователь принудительно выполняет повторную регистрацию при входе в систему. Помните, что не использующие браузер приложения продолжат работать, если пользователь имеет пароли приложений для них.  Кроме того, вы можете удалить пароли приложений пользователей, выбрав параметр **Удалить все существующие пароли приложений, созданные выбранными пользователями**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Как потребовать у пользователей сообщать о способах связи еще раз
1. Войдите на классический портал Azure.
2. В левой части щелкните Active Directory.
3. В разделе каталогов выберите каталог для пользователя, у которого необходимо затребовать повторное предоставление способа связи.
4. В верхней части щелкните "Пользователи".
5. В нижней части страницы щелкните "Управление многофакторной проверкой подлинности" Откроется страница многофакторной проверки подлинности.
6. Выберите пользователя, параметры которого необходимо изменить, и установите флажок рядом с его именем. Возможно, потребуется изменить представление в верхней части страницы.
7. Справа отобразится ссылка **Управление параметрами пользователя** . Нажмите ее.
8. Установите флажок **Требовать у выбранных пользователей сообщать о способах связи еще раз**.
   ![Сообщение о способах связи](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. Щелкните "Сохранить".
10. Нажмите кнопку «Закрыть».

## <a name="delete-users-existing-app-passwords"></a>Удалять существующие пароли приложений пользователей
Это действие удаляет все созданные пользователем пароли приложений. Работа не использующих браузер приложений, связанных с этими паролями, приостанавливается, пока не будут созданы новые пароли.

### <a name="how-to-delete-users-existing-app-passwords"></a>Как удалить существующие пароли приложений пользователей
1. Войдите на классический портал Azure.
2. В левой части щелкните Active Directory.
3. В разделе каталогов выберите каталог пользователя, пароли которого необходимо удалить.
4. В верхней части щелкните "Пользователи".
5. В нижней части страницы щелкните "Управление многофакторной проверкой подлинности" Откроется страница многофакторной проверки подлинности.
6. Выберите пользователя, параметры которого необходимо изменить, и установите флажок рядом с его именем. Возможно, потребуется изменить представление в верхней части страницы.
7. Справа отобразится ссылка **Управление параметрами пользователя** . Нажмите ее.
8. Установите флажок **Удалить все существующие пароли приложений, созданные выбранными пользователями**.
   ![Удаление паролей приложений](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Щелкните "Сохранить".
10. Нажмите кнопку Close (Закрыть).

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Восстановление MFA на всех запомненных устройствах пользователя
Служба Многофакторной идентификации Azure позволяет помечать устройства как доверенные. Дополнительные сведения см. в разделе о [сохранении данных Многофакторной идентификации для устройств, которым доверяют пользователи](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Пользователи могут отключить двухфакторную проверку подлинности на своих устройствах на определенное число дней. В случае потери доверенного устройства или компрометации учетной записи вам потребуется удалить статус доверия и повторно выполнить двухфакторную проверку подлинности.

Параметр **Восстановить многофакторную проверку подлинности на всех запомненных устройствах** означает, что при следующем входе в систему пользователь должен будет выполнить двухфакторную проверку подлинности вне зависимости, помечено его устройство как доверенное или нет. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Как восстановить Multi-Factor Authentication на всех приостановленных устройствах для пользователя
1. Войдите на классический портал Azure.
2. В левой части щелкните Active Directory.
3. В разделе каталогов выберите каталог пользователя, для которого необходимо восстановить проверку Multi-Factor Authentication.
4. В верхней части щелкните "Пользователи".
5. В нижней части страницы щелкните "Управление многофакторной проверкой подлинности" Откроется страница многофакторной проверки подлинности.
6. Выберите пользователя, параметры которого необходимо изменить, и установите флажок рядом с его именем. Возможно, потребуется изменить представление в верхней части страницы.
7. Справа отобразится ссылка **Управление параметрами пользователя** . Нажмите ее.
8. Установите флажок **Восстановить многофакторную проверку подлинности на всех запомненных устройствах**
   .![Удаление паролей приложения](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Щелкните "Сохранить".
10. Нажмите кнопку Close (Закрыть).

