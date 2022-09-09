---
title: Provision throughput on Azure Cosmos DB Gremlin API resources
description: Learn how to provision container, database, and autoscale throughput in Azure Cosmos DB Gremlin API resources. You will use Azure portal, CLI, PowerShell and various other SDKs. 
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
---

# Provision database, container or autoscale throughput on Azure Cosmos DB Gremlin API resources

This article explains how to provision throughput in Azure Cosmos DB Gremlin API. You can provision standard(manual) or autoscale throughput on a container, or a database and share it among the containers within the database. You can provision throughput using Azure portal, Azure CLI, or Azure Cosmos DB SDKs.

If you are using a different API, see [SQL API](how-to-provision-container-throughput.md), [Cassandra API](how-to-provision-throughput-cassandra.md), [API for MongoDB](how-to-provision-throughput-mongodb.md) articles to provision the throughput.

## <a id="portal-gremlin"></a> Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. [Create a new Azure Cosmos account](create-mongodb-dotnet.md#create-a-database-account), or select an existing Azure Cosmos account.

1. Open the **Data Explorer** pane, and select **New Graph**. Next, provide the following details:

   * Indicate whether you are creating a new database or using an existing one. Select the **Provision database throughput** option if you want to provision throughput at the database level.
   * Enter a graph ID.
   * Enter a partition key value (for example, `/ItemID`).
   * Enter a throughput that you want to provision (for example, 1000 RUs).
   * Select **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Screenshot of Data Explorer, when creating a new graph with database level throughput":::

## .NET SDK

> [!Note]
> Use the Cosmos SDKs for SQL API to provision throughput for all Azure Cosmos DB APIs, except Cassandra and MongoDB API.

### Provision container level throughput

# [.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# [.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

### Provision database level throughput

# [.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# [.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## Azure Resource Manager

Azure Resource Manager templates can be used to provision autoscale throughput on database or container-level resources for all Azure Cosmos DB APIs. See [Azure Resource Manager templates for Azure Cosmos DB](templates-samples-gremlin.md) for samples.

## Azure CLI

Azure CLI can be used to provision autoscale throughput on a database or container-level resources for all Azure Cosmos DB APIs. For samples see [Azure CLI Samples for Azure Cosmos DB](cli-samples-gremlin.md).

## Azure PowerShell

Azure PowerShell can be used to provision autoscale throughput on a database or container-level resources for all Azure Cosmos DB APIs. For samples see [Azure PowerShell samples for Azure Cosmos DB](powershell-samples-gremlin.md).

## Next steps

See the following articles to learn about throughput provisioning in Azure Cosmos DB:

* [Request units and throughput in Azure Cosmos DB](request-units.md)