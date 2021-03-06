Теперь вы можете добавить данные в новую коллекцию с помощью обозревателя данных.

1. В обозревателе данных новая база данных отображается в области "Коллекции". Разверните базу данных **Items** и коллекцию **ToDoList**, выберите **Документы**, а затем щелкните **New Documents** (Новые документы). 

   ![Создание документов в обозревателе данных на портале Azure](./media/cosmosdb-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Теперь добавьте несколько новых документов в коллекцию, вставляя уникальные значения идентификатора в каждом документе и изменяя другие свойства по своему усмотрению. Новые документы могут иметь любую структуру, так как Azure Cosmos DB не устанавливает определенные схемы данных.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Теперь в обозревателе данных можно выполнить запросы на получение данных. По умолчанию, чтобы получить все документы в коллекции, обозреватель данных использует запрос SELECT * FROM c, но вы можете изменить его на SELECT * FROM c ORDER BY c.name ASC. Это позволит вернуть все документы в алфавитном порядке. 
 
     Вы также можете использовать обозреватель данных для создания хранимых процедур, определяемых пользователем функций и триггеров, чтобы реализовать бизнес-логику на стороне сервера. Этот инструмент позволяет использовать все встроенные возможности программного доступа к данным, доступные в API-интерфейсах, к которым вы можете быстро получить доступ на портале Azure.