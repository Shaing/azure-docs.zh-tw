---
title: 在 Azure Cosmos DB 中建立容器
description: 瞭解如何使用 Azure 入口網站、.Net、JAVA、Python、node.js 和其他 Sdk，在 Azure Cosmos DB 中建立容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873704"
---
# <a name="create-an-azure-cosmos-container"></a>建立 Azure Cosmos 容器

本文說明建立 Azure Cosmos 容器 (集合、資料表或圖形) 的不同方式。 您可使用 Azure 入口網站、Azure CLI 或支援的 SKD。 本文將示範如何建立容器、指定分割區索引鍵，以及佈建輸送量。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 入口網站建立容器

### <a id="portal-sql"></a>SQL API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有帳戶。

1. 開啟 [**資料總管**] 窗格，然後選取 [**新增容器**]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器識別碼。
   * 輸入分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![[資料總管] 窗格的螢幕擷取畫面，其中已反白顯示新的容器](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [**資料總管**] 窗格，然後選取 [**新增容器**]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入容器識別碼。
   * 輸入分區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![適用于 MongoDB 的 Azure Cosmos DB API、[新增容器] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-cassandra-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的 Keyspace，還是使用現有的 Keyspace。
   * 輸入資料表名稱。
   * 輸入屬性並指定主索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![Cassandra API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API 的主索引鍵會作為分割區索引鍵。

### <a id="portal-gremlin"></a>Gremlin API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-graph-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增圖形]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入圖形識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入頂點的分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![Gremlin API 中 [新增圖形] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>資料表 API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-table-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接下來，提供下列詳細資料：

   * 輸入資料表識別碼。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定]。

    ![資料表 API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## 使用 Azure CLI 建立容器<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

下列連結顯示如何使用 Azure CLI 建立 Azure Cosmos DB 的容器資源。

如需所有 Azure Cosmos DB Api 中所有 Azure CLI 範例的清單，請參閱[SQL API](cli-samples.md)、 [CASSANDRA API](cli-samples-cassandra.md)、 [MongoDB api](cli-samples-mongodb.md)、 [Gremlin API](cli-samples-gremlin.md)和[資料表 API](cli-samples-table.md)

* [建立具有 Azure CLI 的容器](manage-with-cli.md#create-a-container)
* [使用 Azure CLI 建立 MongoDB API Azure Cosmos DB 的集合](./scripts/cli/mongodb/create.md)
* [建立具有 Azure CLI 的 Cassandra 資料表](./scripts/cli/cassandra/create.md)
* [建立具有 Azure CLI 的 Gremlin 圖形](./scripts/cli/gremlin/create.md)
* [使用 Azure CLI 建立資料表 API 資料表](./scripts/cli/table/create.md)

## 使用 PowerShell<a id="ps-sql"> </a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"> 建立容器<a id="ps-table"></a>

下列連結顯示如何使用 PowerShell 建立 Azure Cosmos DB 的容器資源。

如需所有 Azure Cosmos DB Api 中所有 Azure CLI 範例的清單，請參閱[SQL API](powershell-samples-sql.md)、 [CASSANDRA API](powershell-samples-cassandra.md)、 [MongoDB api](powershell-samples-mongodb.md)、 [Gremlin API](powershell-samples-gremlin.md)和[資料表 API](powershell-samples-table.md)

* [使用 Powershell 建立容器](manage-with-powershell.md#create-container)
* [使用 Powershell 建立 MongoDB API Azure Cosmos DB 的集合](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [使用 Powershell 建立 Cassandra 資料表](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [使用 Powershell 建立 Gremlin 圖形](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [使用 Powershell 建立資料表 API 資料表](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>使用 .NET SDK 建立容器

### <a id="dotnet-sql-graph"></a>SQL API 和 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 網路通訊協定不了解[要求單位](request-units.md)的概念。 若要建立已佈建輸送量的新集合，請使用 Azure 入口網站或適用於 SQL API 的 Cosmos DB SDK。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)
