---
title: Request Units as a throughput and performance currency in Azure Cosmos DB
description: Learn about how to specify and estimate Request Unit requirements in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.custom: "seo-nov-2020"

---
# Request Units in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB supports many APIs, such as SQL, MongoDB, Cassandra, Gremlin, and Table. Each API has its own set of database operations. These operations range from simple point reads and writes to complex queries. Each database operation consumes system resources based on the complexity of the operation.

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by Request Units (or RUs, for short). Request unit is a performance currency abstracting the system resources such as CPU, IOPS, and memory that are required to perform the database operations supported by Azure Cosmos DB.

The cost to do a point read (i.e. fetching a single item by its ID and partition key value) for a 1 KB item is 1 Request Unit (or 1 RU). All other database operations are similarly assigned a cost using RUs. No matter which API you use to interact with your Azure Cosmos container, costs are always measured by RUs. Whether the database operation is a write, point read, or query, costs are always measured in RUs.

The following image shows the high-level idea of RUs:

:::image type="content" source="./media/request-units/request-units.png" alt-text="Database operations consume Request Units" border="false":::

To manage and plan capacity, Azure Cosmos DB ensures that the number of RUs for a given database operation over a given dataset is deterministic. You can examine the response header to track the number of RUs that are consumed by any database operation. When you understand the [factors that affect RU charges](request-units.md#request-unit-considerations) and your application's throughput requirements, you can run your application cost effectively.

The type of Azure Cosmos account you're using determines the way consumed RUs get charged. There are 3 modes in which you can create an account:

1. **Provisioned throughput mode**: In this mode, you provision the number of RUs for your application on a per-second basis in increments of 100 RUs per second. To scale the provisioned throughput for your application, you can increase or decrease the number of RUs at any time in increments or decrements of 100 RUs. You can make your changes either programmatically or by using the Azure portal. You are billed on an hourly basis for the amount of RUs per second you have provisioned. To learn more, see the [Provisioned throughput](set-throughput.md) article.

   You can provision throughput at two distinct granularities:

   * **Containers**: For more information, see [Provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
   * **Databases**: For more information, see [Provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

2. **Serverless mode**: In this mode, you don't have to provision any throughput when creating resources in your Azure Cosmos account. At the end of your billing period, you get billed for the amount of Request Units that has been consumed by your database operations. To learn more, see the [Serverless throughput](serverless.md) article. 

3. **Autoscale mode**: In this mode, you can automatically and instantly scale the throughput (RU/s) of your database or container based on it's usage, without impacting the availability, latency, throughput, or performance of the workload. This mode is well suited for mission-critical workloads that have variable or unpredictable traffic patterns, and require SLAs on high performance and scale. To learn more, see the [autoscale throughput](provision-throughput-autoscale.md) article. 

## Request Unit considerations

While you estimate the number of RUs consumed by your workload, consider the following factors:

* **Item size**: As the size of an item increases, the number of RUs consumed to read or write the item also increases.

* **Item indexing**: By default, each item is automatically indexed. Fewer RUs are consumed if you choose not to index some of your items in a container.

* **Item property count**: Assuming the default indexing is on all properties, the number of RUs consumed to write an item increases as the item property count increases.

* **Indexed properties**: An index policy on each container determines which properties are indexed by default. To reduce the RU consumption for write operations, limit the number of indexed properties.

* **Data consistency**: The strong and bounded staleness consistency levels consume approximately two times more RUs while performing read operations when compared to that of other relaxed consistency levels.

* **Type of reads**: Point reads cost significantly fewer RUs than queries.

* **Query patterns**: The complexity of a query affects how many RUs are consumed for an operation. Factors that affect the cost of query operations include: 
 
  * The number of query results
  * The number of predicates
  * The nature of the predicates
  * The number of user-defined functions
  * The size of the source data
  * The size of the result set
  * Projections

  The same query on the same data will always costs the same number of RUs on repeated executions.

* **Script usage**: As with queries, stored procedures and triggers consume RUs based on the complexity of the operations that are performed. As you develop your application, inspect the [request charge header](./optimize-cost-reads-writes.md#measuring-the-ru-charge-of-a-request) to better understand how much RU capacity each operation consumes.

## Request units and multiple regions

If you provision *'R'* RUs on a Cosmos container (or database), Cosmos DB ensures that *'R'* RUs are available in *each* region associated with your Cosmos account. You can't selectively assign RUs to a specific region. The RUs provisioned on a Cosmos container (or database) are provisioned in all the regions associated with your Cosmos account.

Assuming that a Cosmos container is configured with *'R'* RUs and there are *'N'* regions associated with the Cosmos account, the total RUs available globally on the container = *R* x *N*.

Your choice of [consistency model](consistency-levels.md) also affects the throughput. You can get approximately 2x read throughput for the more relaxed consistency levels (e.g., *session*, *consistent prefix* and *eventual* consistency) compared to stronger consistency levels (e.g., *bounded staleness* or *strong* consistency).

## Next steps

- Learn more about how to [provision throughput on Azure Cosmos containers and databases](set-throughput.md).
- Learn more about [serverless on Azure Cosmos DB](serverless.md).
- Learn more about [logical partitions](./partitioning-overview.md).
- Learn how to [provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
- Learn how to [provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).
- Learn how to [find the request unit charge for an operation](find-request-unit-charge.md).
- Learn how to [optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md).
- Learn how to [optimize reads and writes cost in Azure Cosmos DB](optimize-cost-reads-writes.md).
- Learn how to [optimize query cost in Azure Cosmos DB](./optimize-cost-reads-writes.md).
- Learn how to [use metrics to monitor throughput](use-metrics.md).
