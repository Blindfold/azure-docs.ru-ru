---
title: "Как использовать хранилище таблиц из Python | Документация Майкрософт"
description: "Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL)."
services: storage
documentationcenter: python
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 34d075658514045e28d6a784c579528bb3eac376
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-use-table-storage-from-python"></a>Использование табличного хранилища из Python
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища таблиц Azure. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python). Здесь описаны такие сценарии, как создание и удаление таблицы, а также вставка и запрос сущностей в таблице.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-table"></a>Создание таблицы
Объект **TableService** позволяет работать со службами таблиц. Следующий код создает объект **TableService** . Добавьте этот код в начало любого файла Python, из которого планируется получать доступ к службе хранилища Azure программным способом:

```python
from azure.storage.table import TableService, Entity
```

Следующий код создает объект **TableService** , используя имя и ключ доступа учетной записи хранения.  Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Чтобы добавить сущность, сначала создайте словарь или сущность, которая определяет имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей. Вы можете выполнить запрос этих значений гораздо быстрее, чем других свойств. Система использует **PartitionKey** , чтобы автоматически распространять сущности таблиц на множество узлов хранилища.
Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

Чтобы добавить сущность в таблицу, передайте объект словаря в метод **insert\_entity**.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Также можно передать экземпляр класса **Entity** в метод **insert\_entity**.

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '2'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

## <a name="update-an-entity"></a>Обновление сущности
Этот код показывает, как заменить старую версию имеющейся сущности на обновленную версию.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

Если обновляемая сущность отсутствует, операция обновления завершается ошибкой. Если вам необходимо сохранить сущность независимо от того, существовала ли она ранее, следует использовать метод **insert\_or\_replace_entity**.
В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')

task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task, content_type='application/atom+xml')
```

## <a name="change-a-group-of-entities"></a>Изменение группы сущностей
Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте класс **TableBatch** . Если требуется отправить пакет, вызовите метод **commit\_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task10)
batch.insert_entity(task11)
table_service.commit_batch('tasktable', batch)
```

Для пакетов также можно использовать синтаксис диспетчера контекста:

```python
task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task12)
    batch.insert_entity(task13)
```

## <a name="query-for-an-entity"></a>Запрос сущности
Чтобы запросить сущность в таблице, используйте метод **get\_entity**, передав значения **PartitionKey** и **RowKey**.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Запрос набора сущностей
Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности
Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется *проекцией*, снижает потребление полосы пропускания и может повысить производительность запросов, особенно для крупных сущностей. Используйте параметр **select** и передайте имена свойств, которые необходимо передать клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

> [!NOTE]
> Следующий фрагмент работает только для службы хранилища Azure. Его не поддерживает эмулятор хранения.
>
>

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Удаление сущности
Сущность можно удалить с помощью ее ключей раздела и строки.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '1')
```

## <a name="delete-a-table"></a>Удаление таблицы
Следующий код удаляет таблицу из учетной записи хранения.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Дальнейшие действия

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
* [Центр по разработке для Python](/develop/python/)
* [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [пакет SDK для службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python)
