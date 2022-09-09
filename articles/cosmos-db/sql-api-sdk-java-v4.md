---
title: 'Azure Cosmos DB Java SDK v4 for SQL API release notes and resources'
description: Learn all about the Azure Cosmos DB Java SDK v4 for SQL API and SDK including release dates, retirement dates, and changes made between each version of the Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma

---
# Azure Cosmos DB Java SDK v4 for Core (SQL) API: release notes and resources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

The Azure Cosmos DB Java SDK v4 for Core (SQL) combines an Async API and a Sync API into one Maven artifact. The v4 SDK brings enhanced performance, new API features, and Async support based on Project Reactor and the [Netty library](https://netty.io/). Users can expect improved performance with Azure Cosmos DB Java SDK v4 versus the [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) and the [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> These Release Notes are for Azure Cosmos DB Java SDK v4 only. If you are currently using an older version than v4, see the [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) guide for help upgrading to v4.
>
> Here are three steps to get going fast!
> 1. Install the [minimum supported Java runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) so you can use the SDK.
> 2. Work through the [Quickstart Guide for Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) which gets you access to the Maven artifact and walks through basic Azure Cosmos DB requests.
> 3. Read the Azure Cosmos DB Java SDK v4 [performance tips](performance-tips-java-sdk-v4-sql.md) and [troubleshooting](troubleshoot-java-sdk-v4-sql.md) guides to optimize the SDK for your application.
>
> The [Azure Cosmos DB workshops and labs](https://aka.ms/cosmosworkshop) are another great resource for learning how to use Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **SDK Download** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API documentation** | [Java API reference documentation](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Contribute to SDK** | [Azure SDK for Java Central Repo on GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Get started** | [Quickstart: Build a Java app to manage Azure Cosmos DB SQL API data](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [GitHub repo with quickstart code](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Basic code samples** | [Azure Cosmos DB: Java examples for the SQL API](sql-api-java-sdk-samples.md) · [GitHub repo with sample code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Console app with Change Feed**| [Change feed - Java SDK v4 sample](create-sql-api-java-changefeed.md) · [GitHub repo with sample code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web app sample**| [Build a web app with Java SDK v4](sql-api-java-application.md) · [GitHub repo with sample code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Performance tips**| [Performance tips for Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Troubleshooting** | [Troubleshoot Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrate to v4 from an older SDK** | [Migrate to Java V4 SDK](migrate-java-v4-sdk.md) |
| **Minimum supported runtime**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops and labs** |[Cosmos DB workshops home page](https://aka.ms/cosmosworkshop)

