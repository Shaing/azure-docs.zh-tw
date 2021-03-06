---
title: 使用來建立適用于 Azure 資料總管的事件中樞資料連線C#
description: 在本文中，您將瞭解如何使用C#來建立 Azure 資料總管的事件中樞資料連線。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667686"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>使用來建立適用于 Azure 資料總管的事件中樞資料連線C#

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)

「Azure 資料總管」是一項快速又彈性極佳的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料總管可從事件中樞、IoT 中樞和寫入 blob 容器的 blob，提供內嵌（資料載入）。 在本文中，您會使用C#來建立 Azure 資料總管的事件中樞資料連線。

## <a name="prerequisites"></a>必要條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立叢集[和資料庫](create-cluster-database-csharp.md)
* 建立[資料表和資料行對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* 設定[資料庫和資料表原則](database-table-policies-csharp.md)（選擇性）
* 建立[包含資料的事件中樞以進行](ingest-data-event-hub.md#create-an-event-hub)內嵌。 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>新增事件中樞資料連線

下列範例會示範如何以程式設計方式新增事件中樞資料連線。 請參閱[連接到事件中樞](ingest-data-event-hub.md#connect-to-the-event-hub)，以使用 Azure 入口網站新增事件中樞資料連線。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| clientSecret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可以存取您租使用者中的資源。|
| resourceGroupName | *testrg* | 包含您叢集的資源組名。|
| clusterName | *mykustocluster* | 叢集的名稱。|
| databaseName | *mykustodatabase* | 叢集中的目標資料庫名稱。|
| dataConnectionName | *myeventhubconnect* | 所需的資料連線名稱。|
| tableName | *StormEvents* | 目標資料庫中目標資料表的名稱。|
| mappingRuleName | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| dataFormat | *csv* | 訊息的資料格式。|
| eventHubResourceId | *資源識別碼* | 您的事件中樞的資源識別碼，其中包含用於內嵌的資料。 |
| consumerGroup | *$Default* | 事件中樞的取用者群組。|
| location | *美國中部* | 資料連線資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
