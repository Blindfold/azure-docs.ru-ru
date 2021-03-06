---
title: "Настройка параметров группы с помощью командлетов Azure Active Directory | Документация Майкрософт"
description: "Управление параметрами групп с помощью командлетов Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 49ba7e6d5d67b109632b08ce936357804c80da40
ms.lasthandoff: 04/27/2017


---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Настройка параметров групп с помощью командлетов Azure Active Directory

> [!IMPORTANT]
> Это содержимое относится только к единым группам, также известным как группы Office 365. В настоящее время эти командлеты находятся на этапе общедоступной предварительной версии.

Параметры групп Office 365 настраиваются с помощью объектов Settings и SettingsTemplate. Изначально в каталоге не отображаются объекты Settings. Это означает, что каталог настроен с параметрами по умолчанию. Чтобы изменить параметры по умолчанию, необходимо с помощью шаблона параметров создать объект параметров. Шаблоны параметров определены корпорацией Майкрософт. Поддерживается несколько разных шаблонов параметров. Для настройки параметров группы для каталога будет использован шаблон Group.Unified. Для настройки параметров отдельной группы используйте шаблон Group.Unified.Guest. Этот шаблон используется для управления гостевым доступом к группе. 

Командлеты являются частью модуля PowerShell версии 2 для Azure Active Directory. Дополнительные сведения об этом модуле и инструкции по его скачиванию и установке на компьютер см. в разделе [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (PowerShell версии 2 для Azure Active Directory). Обратите внимание, что так как сейчас эти командлеты находятся на этапе общедоступной предварительной версии, потребуется установить предварительный выпуск модуля, который можно найти [здесь](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.85).

## <a name="create-settings-at-the-directory-level"></a>Создание параметров на уровне каталога
Далее приведены действия, необходимые для создания параметров на уровне каталога, которые применяются ко всем единым группам в каталоге.

1. В командлетах DirectorySettings потребуется указать идентификатор объекта SettingsTemplate, который вы хотите использовать. Если он вам неизвестен, выполните приведенный ниже командлет, чтобы отобразить все шаблоны параметров.
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Этот командлет отображает все шаблоны, которые доступны.
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Чтобы добавить URL-адрес правил использования, сначала необходимо получить объект SettingsTemplate, который определяет значение URL-адреса правил использования; это — шаблон Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Затем создайте новый объект параметров на основе этого шаблона:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Затем обновите значение правил использования:
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. И, наконец, примените параметры:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $settings
  ```

После успешного выполнения командлет возвращает идентификатор нового объекта Settings.
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
Ниже приведены параметры, определенные в объекте SettingsTemplate шаблона Group.Unified.

| **Параметр** | **Описание** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Тип: логический<li>значение по умолчанию: True |Флаг, указывающий, разрешено ли создание объединенных групп в каталоге. |
|  <ul><li>GroupCreationAllowedGroupId<li>Тип: строка<li>Default: “” |Идентификатор GUID группы безопасности, участникам которой разрешено создавать единые группы, даже когда EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Тип: строка<li>Default: “” |Ссылка на правила использования группы. |
|  <ul><li>ClassificationDescriptions<li>Тип: строка<li>Default: “” | Разделенный запятыми список описаний классификаций. |
|  <ul><li>DefaultClassification<li>Тип: строка<li>Default: “” | Классификация, которая должна использоваться по умолчанию для группы, если не была указана иная классификация.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Тип: строка<li>Default: “” |Еще не реализован.
|  <ul><li>AllowGuestsToBeGroupOwner<li>Тип: логический<li>значение по умолчанию: False | Логическое значение, указывающее, может ли гостевой пользователь быть владельцем группы. |
|  <ul><li>AllowGuestsToAccessGroups<li>Тип: логический<li>значение по умолчанию: True | Логическое значение, указывающее, может ли гостевой пользователь иметь доступ к содержимому единой группы. |
|  <ul><li>GuestUsageGuidelinesUrl<li>Тип: строка<li>Default: “” | URL-адрес ссылки на правила использования гостя. |
|  <ul><li>AllowToAddGuests<li>Тип: логический<li>значение по умолчанию: True | Логическое значение, указывающее, разрешено ли добавлять гостей в этот каталог.|
|  <ul><li>ClassificationList<li>Тип: строка<li>Default: “” |Разделенный запятыми список допустимых значений классификации, которые можно применять к объединенным группам. |
|  <ul><li>EnableGroupCreation<li>Тип: логический<li>значение по умолчанию: True | Логическое значение, указывающее, могут ли пользователи без прав администратора создавать единые группы. |


## <a name="read-settings-at-the-directory-level"></a>Чтение параметров на уровне каталога
Далее приведены действия, необходимые для чтения параметров на уровне каталога, применимые ко всем группам Office в каталоге.

1. Чтение всех существующих параметров каталога:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Этот командлет возвращает список всех параметров каталога:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Чтение всех параметров определенной группы:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Чтение всех значений параметров каталога из объекта Settings конкретного каталога по его идентификатору GUID.
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Этот командлет возвращает имена и значения в объекте Settings для этой конкретной группы:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Обновление параметров определенной группы

1. Найдите шаблон параметров Groups.Unified.Guest.
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Получите объект SettingTemplate для шаблона Groups.Unified.Guest.
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Создайте объект Settings из шаблона.
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Установите требуемое значение для параметра.
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Создайте параметр для требуемой группы в каталоге.
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Обновление параметров на уровне каталога

Далее приведены действия, необходимые для обновления параметров на уровне каталога, которые применяются ко всем единым группам в каталоге. В этих примерах предполагается, что в каталоге уже существует объект Settings.

1. Найдите существующий объект Settings.
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Обновление значения:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Обновление параметра:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Удаление параметров на уровне каталога
Далее приведено действие, необходимое для удаления параметров на уровне каталога, применимое ко всем группам Office в каталоге.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Справочник по синтаксису командлетов
Дополнительную документацию по PowerShell Azure Active Directory см. в разделе [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0) (Командлеты Azure Active Directory).

## <a name="additional-reading"></a>Дополнительные материалы

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

