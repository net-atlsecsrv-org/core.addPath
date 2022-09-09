---
title: Find request unit (RU) charge for a Cassandra API query in Azure Cosmos DB
description: Learn how to find the request unit (RU) charge for Cassandra queries executed against an Azure Cosmos container. You can use the Azure portal, .NET and Java drivers to find the RU charge. 
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
---
# Find the request unit charge for operations executed in Azure Cosmos DB Cassandra API

Azure Cosmos DB supports many APIs, such as SQL, MongoDB, Cassandra, Gremlin, and Table. Each API has its own set of database operations. These operations range from simple point reads and writes to complex queries. Each database operation consumes system resources based on the complexity of the operation.

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by Request Units (or RUs, for short). You can think of RUs as a performance currency abstracting the system resources such as CPU, IOPS, and memory that are required to perform the database operations supported by Azure Cosmos DB. No matter which API you use to interact with your Azure Cosmos container, costs are always measured by RUs. Whether the database operation is a write, point read, or query, costs are always measured in RUs. To learn more, see the [request units and it's considerations](request-units.md) article.

This article presents the different ways you can find the [request unit](request-units.md) (RU) consumption for any operation executed against a container in Azure Cosmos DB Cassandra API. If you are using a different API, see [API for MongoDB](find-request-unit-charge-mongodb.md), [SQL API](find-request-unit-charge.md), [Gremlin API](find-request-unit-charge-gremlin.md), and [Table API](find-request-unit-charge-table.md) articles to find the RU/s charge.

When you perform operations against the Azure Cosmos DB Cassandra API, the RU charge is returned in the incoming payload as a field named `RequestCharge`. You have multiple options for retrieving the RU charge.

## Use the .NET SDK

When you use the [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/), you can retrieve the incoming payload under the `Info` property of a `RowSet` object:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

For more information, see [Quickstart: Build a Cassandra app by using the .NET SDK and Azure Cosmos DB](create-cassandra-dotnet.md).

## Use the Java SDK

When you use the [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), you can retrieve the incoming payload by calling the `getExecutionInfo()` method on a `ResultSet` object:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

For more information, see [Quickstart: Build a Cassandra app by using the Java SDK and Azure Cosmos DB](create-cassandra-java.md).

## Next steps

To learn about optimizing your RU consumption, see these articles:

* [Request units and throughput in Azure Cosmos DB](request-units.md)
* [Optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimize query cost in Azure Cosmos DB](./optimize-cost-reads-writes.md)