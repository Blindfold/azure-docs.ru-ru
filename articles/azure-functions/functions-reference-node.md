---
title: "Справочник разработчика JavaScript для Функций Azure | Документация Майкрософт"
description: "Узнайте, как разрабатывать функции с помощью JavaScript."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/06/2017
ms.author: chrande, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: ff8a92c66303c81075c8a42baaa841301d65daf1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/01/2017


---
# <a name="azure-functions-javascript-developer-guide"></a>Руководство разработчика JavaScript для Функций Azure
> [!div class="op_single_selector"]
> * [Сценарий C#](functions-reference-csharp.md)
> * [Скрипт F#](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

Интерфейс JavaScript для Azure Functions позволяет легко экспортировать функцию, которая передается в качестве объекта `context`, для взаимодействия со средой выполнения, а также для получения и отправки данных с помощью привязок.

В этой статье предполагается, что вы уже прочли [справочник разработчика по Функциям Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Экспорт функции
Все функции JavaScript должны экспортировать одну `function` с помощью `module.exports`, чтобы среда выполнения могла найти эту функцию и запустить ее. Эта функция должна всегда включать объект `context` .

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Привязки `direction === "in"` передаются как аргументы функции, то есть вы можете использовать [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) для динамической обработки новых входных данных (например, используя `arguments.length` для итерации всех входных данных). Эта возможность удобна, когда у вас есть только триггер без дополнительных входных данных, так как к данным триггера можно обращаться напрямую, без ссылки на объект `context`.

Аргументы всегда передаются в функцию в порядке их появления в файле *function.json*, даже если они не указаны в инструкциях экспорта. Например, если у вас есть функция `function(context, a, b)` и вы изменяете ее на `function(context, a)`, то значение `b` все равно можно получить в коде функции с помощью `arguments[3]`.

Все привязки, независимо от направления, также передаются в объекте `context` (см. указанный ниже скрипт). 

## <a name="context-object"></a>Объект context
Среда выполнения использует объект `context` для передачи данных в функцию и из нее, а также для взаимодействия со средой выполнения.

Объект context всегда является первым параметром функции, и его надо указывать всегда, так как он содержит методы, необходимые для правильного использования среды выполнения, такие как `context.done` и `context.log`. Для этого объекта можно указать любое имя (например, `ctx` или `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Свойство context.bindings

```
context.bindings
```
Возвращает именованный объект, который содержит все входные и выходные данные. Например, следующее определение привязки в *function.json* позволяет вам получить доступ к содержимому очереди с помощью объекта `context.bindings.myInput`. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>Метод context.done
```
context.done([err],[propertyBag])
```

Информирует среду выполнения о завершении выполнения кода. Необходимо вызвать метод `context.done`, или в противном случае среда выполнения не узнает, что функция завершена, и время ожидания выполнения истечет. 

Метод `context.done` позволяет передавать в среду выполнения пользовательское сообщение об ошибке, а также контейнер свойств, которые перезапишут свойства объекта `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Метод context.log  

```
context.log(message)
```
Этот метод позволяет делать записи в потоковые журналы консоли на уровне трассировки по умолчанию. В `context.log` доступны дополнительные методы ведения журнала, позволяющие выполнять запись в журнал консоли на других уровнях трассировки:


| Метод                 | Описание                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Записывает сообщение в журнал на уровне ошибок или более низком.   |
| **warn(_message_)**    | Записывает сообщение в журнал на уровне предупреждений или более низком. |
| **info(_message_)**    | Записывает сообщение в журнал на уровне сведений или более низком.    |
| **verbose(_message_)** | Записывает сообщение в журнал на уровне детализации.           |

Следующий пример записывает в консоль на уровне трассировки "предупреждения":

```javascript
context.log.warn("Something has happened."); 
```
Вы можете задать пороговое значение уровня трассировки для ведения журнала в файле host.json или отключить эту функцию.  Дополнительные сведения о том, как делать записи в журнал, см. в следующем разделе.

## <a name="writing-trace-output-to-the-console"></a>Вывод выходных данных трассировки на консоль 

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. На этом этапе для записи в консоль нельзя использовать метод `console.log`.

При вызове `context.log()` ваше сообщение записывается в консоль на уровне трассировки по умолчанию — уровень _сведений_. Следующий код записывает на консоль на уровне трассировки "сведения":

```javascript
context.log({hello: 'world'});  
```

Предыдущий код аналогичен следующему:

```javascript
context.log.info({hello: 'world'});  
```

Следующий код записывает на консоль на уровне трассировки "ошибки":

```javascript
context.log.error("An error has occurred.");  
```

Так как _уровень ошибок_ является наивысшим уровнем трасировки, эта трассировка записывается в выходные данные на всех уровнях трассировки при условии, что ведение журнала включено.  


Все методы `context.log` поддерживают тот же формат параметров, что и метод Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Просмотрите следующий код, который выполняет запись в консоль, используя уровень трассировки по умолчанию:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Этот же код можно записать в таком формате:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Настройка уровня трассировки для ведения журнала консоли

В Функциях Azure можно определить пороговое значение уровня трассировки для записи в консоль, чтобы легко контролировать, как трассировки записываются в консоль из ваших функций. Чтобы задать пороговое значение для всех трассировок, которые записываются в консоль, используйте свойство `tracing.consoleLevel` в файле host.json. Этот параметр применяется ко всем функциям в приложении-функции. В следующем примере задается пороговое значение трассировки, чтобы включить подробное ведение журнала:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"      
    }
}  
```

Значения **consoleLevel** соответствуют именам методов в `context.log`. Чтобы отключить ведение журнала трассировки в консоли, задайте для параметра **consoleLevel** значение _off_. Дополнительные сведения о файле host.json см. [в этом разделе](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).

## <a name="http-triggers-and-bindings"></a>Триггеры и привязки HTTP

Триггеры HTTP и webhook, а также привязки вывода HTTP используют объекты запроса и ответа для обмена сообщениями HTTP.  

### <a name="request-object"></a>Объект запроса

Объект `request` имеет следующие свойства.

| Свойство      | Описание                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Объект, содержащий текст запроса.               |
| _headers_     | Объект, содержащий заголовок запроса.                   |
| _method_      | Метод HTTP, используемый для запроса.                                |
| _originalUrl_ | URL-адрес запроса.                                        |
| _params_      | Объект, содержащий параметры маршрутизации запроса. |
| _query_       | Объект, содержащий параметры запроса.                  |
| _rawBody_     | Текст сообщения в виде строки.                           |


### <a name="response-object"></a>Объект ответа

Объект `response` имеет следующие свойства.

| Свойство  | Описание                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Объект, содержащий текст ответа.         |
| _headers_ | Объект, содержащий заголовок ответа.             |
| _isRaw_   | Указывает, что форматирование пропускается для ответа.    |
| _состояние_  | Код состояния HTTP ответа.                     |

### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу 

При работе с триггерами HTTP вы можете получить доступ к объектам запроса и ответа HTTP одним из 3 способов:

+ С помощью именованных входных и выходных привязок. В этом случае триггер и привязки HTTP работают как любая другая привязка. В следующем примере объект ответа задается с помощью именованной привязки `response`: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ От свойств `req` и `res` объекта `context`. В этом случае можно использовать обычный шаблон доступа к данным HTTP из объекта context вместо полного шаблона `context.bindings.name`. Следующий пример демонстрирует доступ к объектам `req` и `res` в `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Путем вызова `context.done()`. Специальный тип привязки HTTP возвращает ответ, передавшийся методу `context.done()`. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Эта привязка вывода ожидает предоставления ответа при вызове `done()` следующим образом:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Управление версиями и пакетами Node
Версия Node сейчас зафиксирована в значении `6.5.0`. Мы работаем над тем, чтобы добавить поддержку дополнительных версий и настройки для этих версий.

Следующие шаги позволяют включить пакеты в приложение-функцию: 

1. Перейдите на сайт `https://<function_app_name>.scm.azurewebsites.net`.

2. Щелкните **Debug Console** (Консоль отладки)  > **CMD**.

3. Перейдите к `D:\home\site\wwwroot`, а затем перетащите файл package.json в папку **wwwroot** в верхней части страницы.  
    Существуют другие способы передачи файлов в приложение-функцию. Дополнительные сведения см. в разделе [Как обновить файлы приложения-функции](functions-reference.md#a-idfileupdatea-how-to-update-function-app-files). 

4. После загрузки файла package.json запустите команду `npm install` в **консоли удаленного выполнения Kudu**.  
    В результате этого действия будут загружены пакеты, указанные в файле package.json, и перезапущено приложение-функция.

После установки необходимых пакетов их необходимо импортировать в функцию, вызвав `require('packagename')`, как это показано в следующем примере:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Следует определить файл `package.json` в корне вашего приложения-функции. После этого все функции в приложении будут совместно использовать одни и те же кэшированные пакеты, что обеспечивает наилучшую производительность. При возникновении конфликтов версий их можно разрешить, добавив файл `package.json` в папку определенной функции.  

## <a name="environment-variables"></a>Переменные среды
Чтобы получить значение переменной среды или значение параметра приложения, используйте `process.env`, как показано в следующем примере кода.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в двух следующих разделах.

### <a name="choose-single-core-app-service-plans"></a>Выбор одноядерных планов службы приложений

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для одного ядра вместо плана для нескольких ядер. Сейчас Функции Azure намного эффективнее выполняют функции JavaScript на одноядерных виртуальных машинах. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. При необходимости вы можете вручную добавить дополнительные экземпляры одноядерных виртуальных машин или включить автоматическое масштабирование. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Поддержка TypeScript и CoffeeScript
Так как прямой поддержки автоматической компиляции TypeScript и CoffeeScript в среде выполнения сейчас нет, соответствующую обработку необходимо осуществлять вне среды выполнения во время развертывания. 

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Справочник разработчика C# по функциям Azure](functions-reference-csharp.md)
* [Справочник разработчика F# по Функциям Azure](functions-reference-fsharp.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)


