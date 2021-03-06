---
title: Azure Functions 的 Azure 服務匯流排繫結
description: 瞭解如何在 Azure Functions 中使用「Azure 服務匯流排」觸發程序和繫結。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549065"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服務匯流排繫結

本文說明如何在 Azure Functions 中使用 Azure 服務匯流排繫結。 Azure Functions 支援服務匯流排佇列和主題的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 套件 2.x 版中會提供服務匯流排繫結。 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet 套件 3.x 版會提供服務匯流排繫結。 套件的原始程式碼位於[azure 函式-](https://github.com/Azure/azure-functions-servicebus-extension)驗證程式延伸模組 GitHub 存放庫中。

> [!NOTE]
> 2\.x 版和更新版本不會建立在 `ServiceBusTrigger` 實例中設定的主題或訂用帳戶。 這些版本是以不會處理佇列管理的[Azure](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)為基礎。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>觸發程序

使用服務匯流排觸發程序來回應來自服務匯流排佇列或主題的訊息。 

## <a name="trigger---example"></a>觸發程序 - 範例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列範例示範的 [C# 函式](functions-dotnet-class-library.md)可讀取[訊息中繼資料](#trigger---message-metadata)和記錄服務匯流排佇列訊息：

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列範例示範 function.json 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式可讀取[訊息中繼資料](#trigger---message-metadata)和記錄服務匯流排佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式可讀取[訊息中繼資料](#trigger---message-metadata)和記錄服務匯流排佇列訊息。 

以下是 *function.json* 檔案中的繫結資料：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

以下是 JavaScript 指令碼程式碼：

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例示範如何透過觸發程式來讀取服務匯流排的佇列訊息。

服務匯流排系結定義于*function. json*中，其中*type*設為 `serviceBusTrigger`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

*_\_init_\_* 中的程式碼會將參數宣告為 `func.ServiceBusMessage`，這可讓您讀取函數中的佇列訊息。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列 JAVA 函式會使用 JAVA 函式執行時間連結[庫](/java/api/overview/azure/functions/runtime)中的 `@ServiceBusQueueTrigger` 批註來描述服務匯流排佇列觸發程式的設定。 函式會抓取放在佇列上的訊息，並將它新增至記錄。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

當訊息新增至服務匯流排主題時，也會觸發 JAVA 函式。 下列範例會使用 `@ServiceBusTopicTrigger` 注釋來描述觸發程式設定。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="trigger---attributes-and-annotations"></a>觸發程式-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 類別庫](functions-dotnet-class-library.md)中，使用下列屬性以設定服務匯流排觸發程序：

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  該屬性的建構函式會採用佇列名稱或標題和訂用帳戶。 在 Azure Functions 第 1.x 版中，您也可以指定連線的存取權限。 如果您未指定存取權限，則預設值為 `Manage`。 如需詳細資訊，請參閱[觸發程序 - 組態](#trigger---configuration)一節。

  以下範例顯示搭配字串參數使用的屬性：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  您可以設定 `Connection` 屬性來指定應用程式設定的名稱，其中包含要使用的服務匯流排連接字串，如下列範例所示：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  如需完整範例，請參閱[觸發程式-範例](#trigger---example)。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  提供另一種方式來指定要使用的服務匯流排帳戶。 建構函式會採用內含服務匯流排連接字串的應用程式設定名稱。 屬性可以套用在參數、方法或類別層級。 下列範例所示範的是類別層級與方法層級：

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

要使用的服務匯流排帳戶按以下順序決定：

* `ServiceBusTrigger` 屬性的 `Connection` 內容。
* `ServiceBusAccount` 屬性套用至與 `ServiceBusTrigger` 屬性相同的參數。
* `ServiceBusAccount` 屬性套用至該函式。
* `ServiceBusAccount` 屬性套用至該類別。
* "AzureWebJobsServiceBus" 應用程式設定。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

`ServiceBusQueueTrigger` 注釋可讓您建立在建立服務匯流排佇列訊息時執行的函式。 可用的設定選項包括 [佇列名稱] 和 [連接字串名稱]。

`ServiceBusTopicTrigger` 批註可讓您指定主題和訂用帳戶，以將觸發函式的資料設為目標。

如需詳細資訊，請參閱觸發程式[範例](#trigger---example)。

---

## <a name="trigger---configuration"></a>觸發程式 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `ServiceBusTrigger` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設定為 "serviceBusTrigger"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "in"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列或主題訊息的變數名稱。 |
|**queueName**|**QueueName**|要監視的佇列名稱。  只有在監視佇列時設定 (不適用於主題)。
|**topicName**|**TopicName**|要監視的主題名稱。 只有在監視主題時設定 (不適用於佇列)。|
|**subscriptionName**|**SubscriptionName**|要監視的訂用帳戶名稱。 只有在監視主題時設定 (不適用於佇列)。|
|**connection**|**[連接]**|應用程式設定的名稱包含要用於此繫結的服務匯流排連接字串。 如果應用程式設定名稱是以 "AzureWebJobs" 開頭，您只能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyServiceBus"，則 Functions 執行階段會尋找名稱為 "AzureWebJobsMyServiceBus" 的應用程式設定。 如果您將 `connection` 保留空白，則 Functions 執行階段會使用應用程式設定中名稱為 "AzureWebJobsServiceBus" 的預設服務匯流排連接字串。<br><br>若要取得連接字串，請遵循[取得管理認證](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)所示的步驟。 連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。 |
|**accessRights**|**Access**|連接字串的存取權限。 可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 "listen"。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。 在 Azure Functions 2.x 版和更新版本中，因為最新版的服務匯流排 SDK 不支援管理作業，所以無法使用這個屬性。|
|**isSessionsEnabled**|**IsSessionsEnabled**|如果要連接到[會話感知](../service-bus-messaging/message-sessions.md)的佇列或訂用帳戶，`true`。 否則 `false`，這是預設值。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>觸發程序 - 使用方式

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列是適用于佇列或主題訊息的參數類型：

* `string` - 如果訊息是文字。
* `byte[]` - 適用於二進位資料。
* 自訂類型 - 如果訊息包含 JSON，Azure Functions 會嘗試將 JSON 資料還原序列化。
* `BrokeredMessage`-提供已還原序列化的訊息，其中包含[BrokeredMessage. GetBody\<t > （）](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)方法。

這些參數類型適用于 Azure Functions 1.x 版;若為2.x 和更高版本，請使用[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) ，而不是 `BrokeredMessage`。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列是適用于佇列或主題訊息的參數類型：

* `string` - 如果訊息是文字。
* `byte[]` - 適用於二進位資料。
* 自訂類型 - 如果訊息包含 JSON，Azure Functions 會嘗試將 JSON 資料還原序列化。
* `BrokeredMessage`-提供已還原序列化的訊息，其中包含[BrokeredMessage. GetBody\<t > （）](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)方法。

這些參數適用于 Azure Functions 1.x 版;若為2.x 和更高版本，請使用[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) ，而不是 `BrokeredMessage`。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name from function.json>`存取佇列或主題訊息。 服務匯流排訊息會以字串或 JSON 物件的形式傳遞至函式。

# <a name="pythontabpython"></a>[Python](#tab/python)

佇列訊息可透過輸入為 `func.ServiceBusMessage`的參數提供給函式。 服務匯流排訊息會以字串或 JSON 物件的形式傳遞至函式。

# <a name="javatabjava"></a>[Java](#tab/java)

傳入服務匯流排訊息可透過 `ServiceBusQueueMessage` 或 `ServiceBusTopicMessage` 參數取得。

如需[詳細資訊，請參閱範例](#trigger)。

---

## <a name="trigger---poison-messages"></a>觸發程序 - 有害訊息

無法在 Azure Functions 中控制或設定有害訊息處理。 服務匯流排會自行處理有害訊息。

## <a name="trigger---peeklock-behavior"></a>觸發程序 - PeekLock 行為

Functions 執行階段會在 [PeekLock 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)中收到訊息。 它會在函數成功完成時，於訊息中呼叫 `Complete`，或是在函數失敗時呼叫 `Abandon`。 如果函式執行時間較 `PeekLock` 逾時還長，只要函式正在執行中，就會自動更新鎖定。 

`maxAutoRenewDuration` 在 *host.json* 中是可設定的，其對應至 [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)。 根據服務匯流排文件，此設定允許的上限為 5 分鐘，而您可以將 Functions 時間限制從預設的 5 分鐘增加至 10 分鐘。 您不會想對服務匯流排函式這麼做，因為您會超過服務匯流排更新限制。

## <a name="trigger---message-metadata"></a>觸發程序 - 訊息中繼資料

服務匯流排觸發程序提供數個[中繼資料屬性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 這些屬性可作為其他繫結中繫結運算式的一部分或程式碼中的參數使用。 這些是 [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 類別的屬性。

|屬性|類型|說明|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|傳遞數目。|
|`DeadLetterSource`|`string`|無效信件來源。|
|`ExpiresAtUtc`|`DateTime`|到期時間 (UTC)。|
|`EnqueuedTimeUtc`|`DateTime`|加入佇列的時間 (UTC)。|
|`MessageId`|`string`|服務匯流排可用來識別重複訊息的使用者定義值 (如果已啟用)。|
|`ContentType`|`string`|由傳送者和接收者使用於應用程式特定邏輯的內容類型識別碼。|
|`ReplyTo`|`string`|回覆佇列位址。|
|`SequenceNumber`|`Int64`|由服務匯流排指派給訊息的唯一編號。|
|`To`|`string`|傳送位址。|
|`Label`|`string`|應用程式特定的標籤。|
|`CorrelationId`|`string`|相互關連識別碼。|

> [!NOTE]
> 目前，與已啟用會話的佇列和訂用帳戶搭配使用的服務匯流排觸發程式處於預覽狀態。 請追蹤[此專案](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458)，以取得與此相關的任何進一步更新。 

請參閱稍早在本文中使用這些屬性的[程式碼範例](#trigger---example)。

## <a name="output"></a>輸出

使用 Azure 服務匯流排輸出繫結來傳送佇列或主題訊息。

### <a name="output---example"></a>輸出 - 範例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下列範例示範的 [C# 函式](functions-dotnet-class-library.md)可記錄服務匯流排佇列訊息：

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

下列範例示範 function.json 檔案中的服務匯流排輸出繫結，以及使用此繫結的 [C# 指令碼函式](functions-reference-csharp.md)。 此函式會使用計時器觸發程序，每隔 15 秒傳送一則佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

以下是可建立單一訊息的 C# 指令碼程式碼：

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

以下是可建立多則訊息的 C# 指令碼程式碼：

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下列範例示範 function.json 檔案中的服務匯流排觸發程序繫結，以及使用此繫結的 [JavaScript 函式](functions-reference-node.md)。 此函式會使用計時器觸發程序，每隔 15 秒傳送一則佇列訊息。

以下是 *function.json* 檔案中的繫結資料：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

以下是可建立單一訊息的 JavaScript 指令碼程式碼：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

以下是可建立多則訊息的 JavaScript 指令碼程式碼：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

下列範例示範如何在 Python 中寫出服務匯流排的佇列。

服務匯流排系結定義定義于*function. json*中，其中*type*設為 `serviceBus`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

在 *_\_init_\_. .py*中，您可以藉由將值傳遞給 `set` 方法，將訊息寫出到佇列中。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

下列範例所示範的 Java 函式，會在經由 HTTP 要求觸發時，傳送訊息給服務匯流排佇列 `myqueue`。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 在 [Java 函式執行階段程式庫](/java/api/overview/azure/functions/runtime)中，對其值要寫入至服務匯流排佇列的函式參數使用 `@QueueOutput` 註釋。  參數類型應為 `OutputBinding<T>`，其中 T 是任何原生 Java 類型的 POJO。

JAVA 函數也可以寫入服務匯流排主題。 下列範例會使用 `@ServiceBusTopicOutput` 注釋來描述輸出系結的設定。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="output---attributes-and-annotations"></a>輸出-屬性和注釋

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 類別程式庫](functions-dotnet-class-library.md)中，使用 [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)。

該屬性的建構函式會採用佇列名稱或標題和訂用帳戶。 您也可以指定連線的存取權限。 [輸出 - 組態](#output---configuration)一節說明如何選擇存取權限設定。 以下範例顯示套用至函式傳回值的屬性：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

您可以設定 `Connection` 屬性來指定應用程式設定的名稱，其中包含要使用的服務匯流排連接字串，如下列範例所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

如需完整範例，請參閱[輸出-範例](#output---example)。

您可以使用 `ServiceBusAccount` 屬性來指定要在類別、方法或參數層級使用的服務匯流排帳戶。  如需詳細資訊，請參閱[觸發程序 - 屬性](#trigger---attributes-and-annotations)。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

C#腳本不支援屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支援屬性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支援屬性。

# <a name="javatabjava"></a>[Java](#tab/java)

`ServiceBusQueueOutput` 和 `ServiceBusTopicOutput` 批註可用來將訊息寫入為函式輸出。 以這些注釋裝飾的參數必須宣告為 `OutputBinding<T>`，其中 `T` 是對應至訊息類型的類型。

---

## <a name="output---configuration"></a>輸出 - 設定

下表說明您在 *function.json* 檔案中設定的繫結設定屬性內容和 `ServiceBus` 屬性。

|function.json 屬性 | 屬性內容 |說明|
|---------|---------|----------------------|
|**type** | n/a | 必須設為 "serviceBus"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | n/a | 必須設定為 "out"。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | n/a | 代表函式程式碼中佇列或主題訊息的變數名稱。 設為 "$return" 以參考函式傳回值。 |
|**queueName**|**QueueName**|佇列的名稱。  只有在傳送佇列訊息時設定 (不適用於主題)。
|**topicName**|**TopicName**|主題的名稱。 只有在傳送主題訊息時設定 (不適用於佇列)。|
|**connection**|**[連接]**|應用程式設定的名稱包含要用於此繫結的服務匯流排連接字串。 如果應用程式設定名稱是以 "AzureWebJobs" 開頭，您只能指定名稱的其餘部分。 例如，如果您將 `connection` 設定為 "MyServiceBus"，則 Functions 執行階段會尋找名稱為 "AzureWebJobsMyServiceBus" 的應用程式設定。 如果您將 `connection` 保留空白，則 Functions 執行階段會使用應用程式設定中名稱為 "AzureWebJobsServiceBus" 的預設服務匯流排連接字串。<br><br>若要取得連接字串，請遵循[取得管理認證](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)所示的步驟。 連接字串必須是用於服務匯流排命名空間，而不限於特定佇列或主題。|
|**accessRights**|**Access**|連接字串的存取權限。 可用值為 `manage` 和 `listen`。 預設值是 `manage`，這表示 `connection` 已具備**管理**權限。 如果您使用沒有**管理**權限的連接字串，請將 `accessRights` 設定為 "listen"。 否則，Functions 執行階段在嘗試執行需要管理權限的作業時可能會失敗。 在 Azure Functions 2.x 版和更新版本中，因為最新版的服務匯流排 SDK 不支援管理作業，所以無法使用這個屬性。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>輸出 - 使用方式

在 Azure Functions 1.x 中，執行階段會建立佇列 (如果佇列不存在)，且您已將 `accessRights` 設為 `manage`。 在函數2.x 版和更新版本中，佇列或主題必須已經存在;如果您指定的佇列或主題不存在，此函式將會失敗。 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage`-當函式結束時，如果參數值為 null，函數不會建立訊息（針對函式1.x）
* `out Message`-當函式結束時，如果參數值為 null，函數不會建立訊息（for 函數2.x 和更新版本）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 適用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用C#函數時：

* 非同步函數需要傳回值或 `IAsyncCollector`，而不是 `out` 參數。

* 若要存取會話識別碼，請系結至[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)型別，然後使用 `sessionId` 屬性。

# <a name="c-scripttabcsharp-script"></a>[C#文字](#tab/csharp-script)

針對輸出系結使用下列參數類型：

* `out T paramName` - `T` 可以是任何可序列化 JSON 的類型。 當函式結束時，如果參數值為 Null，則 Functions 會使用 Null 物件建立訊息。
* `out string` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out byte[]` - 當函式結束時，如果參數值為 Null，則 Functions 不會建立一則訊息。
* `out BrokeredMessage`-當函式結束時，如果參數值為 null，函數不會建立訊息（針對函式1.x）
* `out Message`-當函式結束時，如果參數值為 null，函數不會建立訊息（for 函數2.x 和更新版本）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 適用於建立多個訊息。 當您呼叫 `Add` 方法時，就會建立一則訊息。

使用C#函數時：

* 非同步函數需要傳回值或 `IAsyncCollector`，而不是 `out` 參數。

* 若要存取會話識別碼，請系結至[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)型別，然後使用 `sessionId` 屬性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name from function.json>`存取佇列或主題。 您可以將字串、位元組陣列或 JavaScript 物件（還原序列化為 JSON）指派給 `context.binding.<name>`。

# <a name="pythontabpython"></a>[Python](#tab/python)

請使用[AZURE 服務匯流排 SDK](https://docs.microsoft.com/azure/service-bus-messaging) ，而不是內建的輸出系結。

# <a name="javatabjava"></a>[Java](#tab/java)

請使用[AZURE 服務匯流排 SDK](https://docs.microsoft.com/azure/service-bus-messaging) ，而不是內建的輸出系結。

---

## <a name="exceptions-and-return-codes"></a>例外狀況和傳回碼

| 繫結 | 參考 |
|---|---|
| 服務匯流排 | [服務匯流排錯誤碼](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| 服務匯流排 | [服務匯流排限制](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 設定

本節說明2.x 版和更高版本中可供此系結使用的全域設定。 下面的範例 host. json 檔案僅包含此系結的設定。 如需有關全域設定的詳細資訊，請參閱[Azure Functions 版本的 host. json 參考](functions-host-json.md)。

> [!NOTE]
> 有關 Functions 1.x 中 host.json 的參考，請參閱[適用於 Azure Functions 1.x 的 host.json 參考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|屬性  |預設 | 說明 |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|將自動更新訊息鎖定的最大持續時間。|
|autoComplete|true|觸發程式是否應該立即將訊息標示為完成（自動完成），或等待函式順利結束以呼叫 complete。|
|maxConcurrentCalls|16|訊息幫浦應該起始之回呼的並行呼叫數上限。 Functions 執行階段預設會並行處理多個訊息。 若要指示執行階段一次只處理一個佇列或主題訊息，請將 `maxConcurrentCalls` 設定為 1。 |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
