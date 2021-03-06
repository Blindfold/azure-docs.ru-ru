---
title: "Управление образом виртуальной машины в Azure Marketplace | Документация Майкрософт"
description: "Подробное руководство по управлению образом виртуальной машины в Azure Marketplace после начальной публикации."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 766e686170c35f3899e070671654c337d3dc642f
ms.contentlocale: ru-ru
ms.lasthandoff: 04/29/2017


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Руководство по эксплуатации предложений виртуальных машин в Azure Marketplace
В этой статье объясняется, как обновить действующее предложение виртуальной машины в Azure Marketplace. Здесь также описана процедура добавления одного или нескольких новых SKU в имеющееся предложение, а также удаления действующего предложения виртуальной машины или SKU из Azure Marketplace.

Когда предложение или номер SKU развернуто в промежуточной среде на [портале Azure](http://portal.azure.com), невозможно изменить значения следующих полей:

* **Offer Identifier** (Идентификатор предложения): ["Портал публикации" -> "Виртуальные машины" -> выберите предложение -> вкладка "Образы VM" -> Offer Identifier (Идентификатор предложения)];
* **SKU Identifier:** (Идентификатор SKU): [Портал публикации -> Виртуальные машины -> выберите предложение -> SKUs (Номера SKU) -> Add a SKU (Добавить SKU)];
* **Publisher Namespace** (Пространство имен издателя): ["Портал публикации" -> "Виртуальные машины" -> Walkthrough (Пошаговое руководство) -> Tell Us About Your Company (Расскажите о своей компании) (в разделе Step 2 Register Your Company (Шаг 2. Регистрация компании)) -> Publisher Namespace (Пространство имен издателя) -> "Пространство имен"].

Когда предложение или номер SKU внесены в список [Azure Marketplace](http://azure.microsoft.com/marketplace), невозможно изменить значения следующих полей:

* **Offer Identifier** (Идентификатор предложения): ["Портал публикации" -> "Виртуальные машины" -> выберите предложение -> вкладка "Образы VM" -> Offer Identifier (Идентификатор предложения)];
* **SKU Identifier:** (Идентификатор SKU): [Портал публикации -> Виртуальные машины -> выберите предложение -> SKUs (Номера SKU) -> Add a SKU (Добавить SKU)];
* **Publisher Namespace:** (Пространство имен издателя): [Портал публикации -> Виртуальные машины -> Walkthrough (Пошаговое руководство) -> Tell Us About Your Company (Расскажите о своей компании) (в разделе Step 2 Register (Шаг 2. Регистрация)) -> Publisher Namespace (Пространство имен издателя) -> Пространство имен];
* **Порты**: ["Портал публикации" -> "Виртуальные машины" -> выберите предложение -> вкладка "Образы VM" -> Open Ports (Открыть порты)];
* **Pricing Change of listed SKU(s)**
* **Billing Model Change of listed SKU(s)**
* **Removal of billing regions of listed SKU(s)**
* **Changing the data disk count of listed SKU(s)**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. Обновление технических сведений SKU
Вы можете добавить новую версию во внесенный в список номер SKU и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **ОБРАЗЫ VM** .
4. В разделе **SKUs** на вкладке **ОБРАЗЫ VM** найдите SKU, который нужно обновить.
5. Затем добавьте номер новой версии SKU и нажмите кнопку **"+"** . Номер новой версии должен быть в формате X.Y.Z, где X, Y и Z являются целыми числами. Версии нужно изменять только пошагово в сторону увеличения.
6. В поле **OS VHD URL** (URL-адрес VHD с ОС) добавьте URI подписанного URL-адреса, созданного для VHD с операционной системой, и сохраните изменения.

   > [!IMPORTANT]
   > Для внесенного в список номера SKU нельзя увеличить или уменьшить значение числа дисков данных. В этом случае необходимо создать новый номер SKU. Подробные указания см. в разделе [3. Добавление нового SKU во внесенное в список предложение](#3-how-to-add-a-new-sku-under-a-live-offer).
   >
   >
7. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
8. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. Обновление нетехнических сведений о предложении или номере SKU
Вы можете обновить нетехнические сведения (маркетинговые, юридические, касающиеся поддержки или категорий) о действующем предложении или SKU в Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1. Обновление логотипов и описания предложения
Вы можете обновить сведения о предложении и повторно опубликовать его, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **МАРКЕТИНГ** .
4. Нажмите кнопку **АНГЛИЙСКИЙ (США)** .
5. В боковом меню слева щелкните вкладку **СВЕДЕНИЯ** . В разделе *ОПИСАНИЕ* на вкладке **СВЕДЕНИЯ** можно обновить заголовок, сводку и развернутую сводку для вашего предложения, а также сохранить изменения.

   > [!NOTE]
   > При обновлении сведений об SKU необходимо учитывать следующее.
   > **Текст в описании предложения и SKU должен быть разным. Текст в заголовке SKU и развернутой сводке предложения должен быть разным. Текст в заголовке SKU и сводке предложения должен быть разным.**
   >
   >

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. В разделе *LOGOS* (Логотипы) на вкладке **Сведения** можно обновить логотипы. Логотипы должны соответствовать [рекомендациям Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (см. раздел "Шаг 1. Предоставление маркетинговых материалов Marketplace" -> "Сведения" -> "Рекомендации по логотипам Azure Marketplace").

   > [!NOTE]
   > Имиджевый логотип необязательный. Его можно не загружать. Однако если вы загрузите этот логотип, вы не сможете удалить его из портала публикации. В этом случае необходимо следовать [рекомендациям к имиджевому логотипу](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (см. раздел "Шаг 1. Предоставление маркетинговых материалов Marketplace" -> "Сведения" -> "Дополнительные рекомендации для имиджевого логотипа (необязательно)").
   >
   >
7. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md).
8. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Обновление описания SKU
Вы можете обновить сведения об SKU и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **МАРКЕТИНГ** .
4. Нажмите кнопку **АНГЛИЙСКИЙ (США)** .
5. В боковом меню слева щелкните вкладку **ПЛАНЫ** . В разделе *SKUs* (Номера SKU) на вкладке **ПЛАНЫ** можно обновить заголовок, сводку и описание SKU, а также сохранить изменения.

   > [!NOTE]
   > При обновлении сведений об SKU необходимо учитывать следующее. **Текст в описании предложения и SKU должен быть разным. Текст в заголовке SKU и развернутой сводке предложения должен быть разным. Текст в заголовке SKU и сводке предложения должен быть разным.**
   >
   >
6. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробного руководства по тестированию предложения в промежуточной среде перейдите по этой ссылке.
7. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3. Изменение имеющихся ссылок или добавление новых
Вы можете изменить имеющиеся ссылки или добавить новые и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **МАРКЕТИНГ** .
4. Нажмите кнопку **АНГЛИЙСКИЙ (США)** .
5. В боковом меню слева щелкните вкладку **ССЫЛКИ** .
6. Чтобы добавить новую ссылку, в разделе *ССЫЛКИ* нажмите кнопку **ДОБАВИТЬ ССЫЛКУ** . Откроется диалоговое окно *ДОБАВЛЕНИЕ ССЫЛКИ* . Здесь можно указать заголовок и URL-адрес для ссылки, а затем сохранить изменения. Вы можете ввести любую ссылку, содержащую сведения, которые могут помочь клиентам.
7. Чтобы обновить или удалить имеющуюся ссылку, выберите необходимую ссылку и нажмите кнопку "Изменить" или "Удалить" соответственно.

   > [!NOTE]
   > Ссылки, добавляемые в этом разделе, должны работать надлежащим образом, так как они проверяются во время обработки запроса на перенос в рабочую среду.
   >
   >
8. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md).
9. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4. Изменение имеющегося примера изображения или добавление нового
Вы можете изменить имеющиеся примеры изображений или добавить новые и повторно опубликовать предложение, выполнив приведенные ниже шаги.

> [!NOTE]
> На странице [https://portal.azure.com](https://portal.azure.com)отображается только один пример изображения.
>
>

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **МАРКЕТИНГ** .
4. Нажмите кнопку **АНГЛИЙСКИЙ (США)** .
5. В боковом меню слева щелкните вкладку **SAMPLE IMAGES** (Примеры изображений).
6. Чтобы добавить новый пример изображения, в разделе *SAMPLE IMAGES* (Примеры изображений) нажмите кнопку **UPLOAD A NEW IMAGE** (Передать новое изображение), а затем сохраните изменения.

   > [!NOTE]
   > Включение примеров изображения — необязательный шаг.
   >
   >
7. Чтобы обновить или удалить имеющийся пример изображения, найдите необходимый пример и нажмите кнопку **ЗАМЕНИТЬ ИЗОБРАЖЕНИЕ** или "Удалить" соответственно.
8. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md).
9. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5. Обновление юридических сведений
Вы можете обновить юридические сведения и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **МАРКЕТИНГ** .
4. Нажмите кнопку **АНГЛИЙСКИЙ (США)** .
5. В боковом меню слева щелкните вкладку **ЮРИДИЧЕСКИЕ СВЕДЕНИЯ** . В разделе *юридических сведений* можно обновить политики и условия использования. Введите или вставьте политики и условия в текстовое поле *Условия использования* и сохраните изменения.
6. Юридические условия использования должны содержать на больше 1 000 000 символов.
7. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
8. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6. Обновление сведений о поддержке
Вы можете обновить сведения о поддержке и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **ПОДДЕРЖКА** .
4. В разделе *Engineering Contact* (Контакты технического отдела) на вкладке **ПОДДЕРЖКА** можно обновить контактные сведения. Эти сведения используются только для внутренней связи между партнером и корпорацией Майкрософт.
5. В разделе *Поддержка пользователей* вкладки **Поддержка** можно обновить контактные сведения службы поддержки, такие как **имя, электронная почта, номер телефона** и **URL-адрес службы поддержки**. Эти сведения используются только для внутренней связи между партнером и корпорацией Майкрософт.

   > [!NOTE]
   > Если вам нужно указать только поддержку по электронной почте, укажите в разделе **Служба поддержки клиентов** фиктивный номер телефона. В этом случае вместо этого номера будет использоваться предоставленный электронный адрес.
   >
   >
6. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
7. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7. Обновление категорий
Вы можете обновить раздел категорий для предложения и повторно опубликовать предложение, выполнив приведенные ниже шаги.

1. Войдите на [портал публикации](https://publish.windowsazure.com)
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **КАТЕГОРИИ** .
4. В разделе *КАТЕГОРИИ* можно обновить категории для предложения и сохранить изменения. Вы можете выбрать не более пяти категорий для коллекции Azure Marketplace.
5. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
6. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. Добавление нового SKU во внесенное в список предложение
Вы можете добавить новый SKU в действующее предложение, выполнив приведенные ниже действия.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **SKUs** (Номера SKU). После этого нажмите кнопку **Add a SKU**(Добавить номер SKU).  Откроется новое диалоговое окно. Введите идентификатор SKU в нижнем регистре. Установите флажок "Bring-your-own billing model (BYOL)" (Использовать собственную модель выставления счетов (BYOL)), если вы хотите опубликовать новый SKU с моделью выставления счетов BYOL. В противном случае снимите флажок для BYOL. После этого щелкните галочку в диалоговом окне, чтобы создать новый SKU. Если вы не выбрали модель выставления счетов BYOL для нового SKU, то модели выставления счетов для нового SKU автоматически будет присвоено значение "Каждый час". Если вы хотите использовать 30-дневную бесплатную пробную версию для почасового выставления счетов, выберите значение "Один месяц" для параметра "Is a free trial available?" (Доступна ли бесплатная пробная версия?). В противном случае выберите "Нет пробной версии". [Примечание. Параметр "Is a free trial available?" (Доступна ли бесплатная пробная версия?) отображается, только если вы не выбрали BYOL в диалоговом окне при создании нового SKU.]

   > [!IMPORTANT]
   > Для параметра Hide this SKU from the Marketplace because it should always be bought via a solution template (Скрывать этот SKU в Marketplace, так как его всегда нужно покупать через шаблон решений) необходимо установить значение "Да", только если вы утверждены для публикации предложения шаблона решений в Azure Marketplace. В противном случае для этого параметра всегда должно быть задано значение "Нет".
   >
   >
4. Теперь в боковом меню слева выберите вкладку **ОБРАЗЫ VM** и найдите новый SKU, которой вы создали.
5. Чтобы настроить новый SKU, перейдите к шагу 5 по этой [ссылке](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) .
6. Чтобы добавить маркетинговые материалы для нового SKU, см. сведения в разделе "Шаг 1. Предоставление маркетинговых материалов Marketplace" -> "Сведения" -> пункты 2–5 по этой [ссылке](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Чтобы добавить сведения о ценах для нового SKU, см. сведения в разделе 2.1. этой статьи. Установите расценки для виртуальной машины, используя эту [ссылку](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. После внесения изменений перейдите на вкладку **Публикация** и нажмите кнопку **Push to staging** (Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
9. После тестирования предложения в промежуточной среде откройте вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. Изменение числа дисков данных для внесенного в список номера SKU
Для внесенного в список номера SKU нельзя увеличить или уменьшить значение числа дисков данных. В этом случае необходимо создать новый номер SKU. Подробные указания см. в разделе [3. Добавление нового SKU в действующее предложение](#3-how-to-add-a-new-sku-under-a-live-offer).

## <a name="5----how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.    Удаление внесенного в список предложения из Azure Marketplace
При удалении действующего предложения нужно выполнить определенные действия. Выполните приведенные ниже шаги, чтобы получить рекомендации от службы поддержки по удалению внесенного в список предложения из Azure Marketplace.

1. Создайте запрос в службу поддержки, используя эту [ссылку](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681).
2. В качестве типа проблемы выберите **Managing offers** (Управление предложениями), а в качестве категории — **Modifying an offer and/or SKU already in production** (Изменение предложения и/или SKU, которые уже находятся в рабочей среде).
3. Отправьте запрос.

Отдел службы поддержки поможет вам в ходе удаления предложения или SKU.

> [!NOTE]
> Вы всегда можете удалить предложение, пока оно находится в состоянии черновика (т. е. не в промежуточной или рабочей среде). Для этого нажмите кнопку **Удалить черновик** на вкладке **Журнал**.
>
>

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. Удаление внесенного в список номера SKU из Azure Marketplace
Вы можете удалить внесенный в список номер SKU из Azure Marketplace, выполнив действия, описанные ниже.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. На боковой панели навигации слева щелкните вкладку **SKUs** (Номера SKU).
4. Выберите номер SKU, который необходимо удалить, и нажмите кнопку "Удалить" напротив этого номера.
5. После этого перейдите на вкладку "ПУБЛИКАЦИЯ" на портале публикации и нажмите кнопку **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.
6. Как только предложение будет повторно опубликовано в Azure Marketplace, номер SKU будет удален из Azure Marketplace и портала Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. Удаление текущей версии внесенного в список номера SKU из Azure Marketplace
Вы можете удалить текущую версию внесенного в список номера SKU из Azure Marketplace, выполнив действия, описанные ниже. По завершении этого процесса будет выполнен откат номера SKU до предыдущей версии.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. На боковой панели навигации слева щелкните вкладку **ОБРАЗЫ VM** .
4. Выберите номер SKU, текущую версию которого необходимо удалить, и нажмите кнопку "Удалить" напротив этой версии.
5. После этого перейдите на вкладку **Публикация** на портале публикации и нажмите кнопку **Request approval to push to production** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.
6. Как только предложение будет повторно опубликовано в Azure Marketplace, текущая версия внесенного в список номера SKU будет удалена из Azure Marketplace и портала Azure. Будет выполнен откат номера SKU до предыдущей версии.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. Возврат рабочих значений цен для внесенных в список элементов
Предположим, мы изменили цены для внесенного в список номера SKU (или удалили регионы выставления счетов для внесенного в список номера SKU). Так как это действие не поддерживается в Azure Marketplace, необходимо отменить наши изменения и вернуть рабочие значения. Как это сделать?

Выполните приведенные ниже действия.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **ЦЕНЫ** .
4. На вкладке "Цены" выберите регион, для которого необходимо выполнить сброс значений цен.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Для номеров SKU с почасовой моделью выставления счетов: выполните сброс цен для всех ядер, так как они находятся в рабочей среде для выбранного региона. Для номеров SKU с моделью выставления счетов BYOL: сделайте номер SKU доступным в регионе, установив флажок напротив этого номера SKU в разделе "EXTERNALLY-LICENSED (BYOL) SKU AVAILABILITY" (Доступность для SKU (BYOL) с внешним лицензированием) (см. снимок экрана ниже).

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Затем нажмите кнопку **AUTOPRICE OTHER MARKETS BASED ON PRICES IN UNITED STATES**(Автоматически назначить цены для других рынков на основе цен в США).

   > [!NOTE]
   > Надпись на этой кнопке может отличаться в зависимости от выбранного региона. Так как мы выбрали США при создании этого документа, то на приведенном ниже снимке экрана кнопка подписана "Auto price other markets based on prices in United States" (Автоматически назначить цены для других рынков на основе цен в США).
   >
   >

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Откроется окно мастера автоматического назначения цен. На первой странице предлагается выбрать базовый рынок. Сделайте свой выбор и перейдите на следующую страницу, нажав кнопку **->**.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. На странице 2 будет предложено выбрать ядра и планы. Выберите необходимые планы и ядра, а затем нажмите кнопку "->".

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. На странице 3 представлены рынки и регионы. Чтобы выбрать все регионы, нажмите кнопку "Toggle All" (Переключить все), или вручную установите флажки напротив необходимых регионов. Нажмите кнопку "->", чтобы перейти на следующую страницу.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. На странице 4 отображается обменный курс валют. Нажмите кнопку "Готово", чтобы завершить процесс. Мастер выполнит сброс значений цен в соответствии с вашим выбором.
11. Теперь перейдите на вкладку "Цены" и нажмите кнопку "VIEW SUMMARY AND CHANGES" (Просмотреть сводку и изменения).
    В разделе "View Version" (Просмотр версии) выберите "Черновик", а в разделе "Сравнить с" выберите "Рабочая среда" (см. снимок экрана ниже). Если вы видите, что цены не отличаются, это значит, что для них успешно возвращены рабочие значения.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. После внесения изменений перейдите на вкладку "ПУБЛИКАЦИЯ" и нажмите кнопку **PUSH TO STAGING**(Переместить в промежуточную среду). Для получения подробных указаний по тестированию предложения в промежуточной среде перейдите по этой [ссылке](marketplace-publishing-vm-image-test-in-staging.md)
13. После тестирования предложения в промежуточной среде откройте вкладку "ПУБЛИКАЦИЯ" на портале публикации и нажмите кнопку **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. Возврат рабочих значений для модели выставления счетов
Предположим, мы изменили модель выставления счетов для внесенного в список номера SKU. Так как это действие не поддерживается в Azure Marketplace, необходимо отменить наши изменения и вернуть рабочие значения. Как это сделать?

Выполните следующие действия.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **SKUs** (Номера SKU).
4. Нажмите кнопку "ИЗМЕНИТЬ", чтобы вернуть предыдущую модель выставления счетов. Откроется окно. В соответствии со своими условиями установите или снимите флажок **Billing and licensing is done externally from Azure (aka Bring Your Own License)** (Выставление счетов и лицензирование осуществляется за пределами Azure (как при использовании собственных лицензий (BYOL))).

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. После выполнения этих действия обратитесь к ответу на вопрос 8 в этом документе, чтобы вернуть предыдущие значения цен.
6. Затем перейдите на вкладку **ПУБЛИКАЦИЯ** на портале публикации и выполните перемещение предложения в промежуточную среду, чтобы протестировать его. По завершении тестирования предложения нажмите кнопку **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. Возврат рабочего значения параметра видимости для внесенного в список номера SKU
Выполните следующие действия.

1. Войдите на [портал публикации](https://publish.windowsazure.com).
2. Перейдите на вкладку **ВИРТУАЛЬНЫЕ МАШИНЫ** и выберите свое предложение.
3. В боковом меню слева щелкните вкладку **SKUs** (Номера SKU).
4. Выберите номер SKU и верните рабочее значение параметра видимости для этого номера.

    ![рисунок](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. По завершении внесения изменений нажмите кнопку **REQUEST APPROVAL TO PUSH TO PRODUCTION** (Запросить утверждение для перемещения в рабочую среду), чтобы повторно опубликовать предложение в Azure Marketplace.

## <a name="see-also"></a>См. также
* [Приступая к работе: как опубликовать предложение в Azure Marketplace](marketplace-publishing-getting-started.md)
* [Основные сведения об отчетах о выплатах](marketplace-publishing-report-payout.md)
* [Как изменить статус участия в программе поощрения поставщиков облачных решений](marketplace-publishing-csp-incentive.md)
* [Решение распространенных проблем с публикацией в Marketplace](marketplace-publishing-support-common-issues.md)
* [Поддержка издателей](marketplace-publishing-get-publisher-support.md)
* [Локальное создание образа виртуальной машины](marketplace-publishing-vm-image-creation-on-premise.md)
* [Создание виртуальной машины под управлением Windows на портале предварительной версии Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

