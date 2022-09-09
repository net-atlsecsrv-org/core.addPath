---
title: What is Azure Cosmos DB Analytical Store (Preview)?
description: Learn about Azure Cosmos DB transactional (row-based) and analytical(column-based) store. Benefits of analytical store, performance impact for large-scale workloads, and auto sync of data from transactional store to analytical store  
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: rosouz
ms.custom: "seo-nov-2020"
---

# What is Azure Cosmos DB analytical store (Preview)?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB analytical store is currently in preview. This preview version is provided without a service level agreement, and it's not recommended for production workloads. For more information, see [Supplemental terms of use for Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB analytical store is a fully isolated column store for enabling large-scale analytics against operational data in your Azure Cosmos DB, without any impact to your transactional workloads. 

Azure Cosmos DB transactional store is schema-agnostic, and it allows you to iterate on your transactional applications without having to deal with schema or index management. In contrast to this, Azure Cosmos DB analytical store is schematized to optimize for analytical query performance. This article describes in detailed about analytical storage.

## Challenges with large-scale analytics on operational data

The multi-model operational data in an Azure Cosmos DB container is internally stored in an indexed row-based "transactional store". Row store format is designed to allow fast transactional reads and writes in the order-of-milliseconds response times, and operational queries. If your dataset grows large, complex analytical queries can be expensive in terms of provisioned throughput on the data stored in this format. High consumption of provisioned throughput in turn, impacts the performance of transactional workloads that are used by your real-time applications and services.

Traditionally, to analyze large amounts of data, operational data is extracted from Azure Cosmos DB's transactional store and stored in a separate data layer. For example, the data is stored in a data warehouse or data lake in a suitable format. This data is later used for large-scale analytics and analyzed using  compute engine such as the Apache Spark clusters. This separation of analytical storage and compute layers from operational data results in additional latency, because the ETL(Extract, Transform, Load) pipelines are run less frequently to minimize the potential impact on your transactional workloads.

The ETL pipelines also become complex when handling updates to the operational data when compared to handling only newly ingested operational data. 

## Column-oriented analytical store

Azure Cosmos DB analytical store addresses the complexity and latency challenges that occur with the traditional ETL pipelines. Azure Cosmos DB analytical store can automatically sync your operational data into a separate column store. Column store format is suitable for large-scale analytical queries to be performed in an optimized manner, resulting in improving the latency of such queries.

Using Azure Synapse Link, you can now build no-ETL HTAP solutions by directly linking to Azure Cosmos DB analytical store from Synapse Analytics. It enables you to run near real-time large-scale analytics on your operational data.

## Features of analytical store 

When you enable analytical store on an Azure Cosmos DB container, a new column-store is internally created based on the operational data in your container. This column store is persisted separately from the row-oriented transactional store for that container. The inserts, updates, and deletes to your operational data are automatically synced to analytical store. You don't need the change feed or ETL to sync the data.

### Column store for analytical workloads on operational data

Analytical workloads typically involve aggregations and sequential scans of selected fields. By storing the data in a column-major order, the analytical store allows a group of values for each field to be serialized together. This format reduces the IOPS required to scan or compute statistics over specific fields. It dramatically improves the query response times for scans over large data sets. 

For example, if your operational tables are in the following format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Example operational table" border="false":::

The row store persists the above data in a serialized format, per row, on the disk. This format allows for faster transactional reads, writes, and operational queries, such as, "Return information about Product1". However, as the dataset grows large and if you want to run complex analytical queries on the data it can be expensive. For example, if you want to get "the sales  trends for a product under the category named 'Equipment' across different business units and months", you need to run a complex query. Large scans on this dataset can get expensive in terms of provisioned throughput and can also impact the performance of the transactional workloads powering your real-time applications and services.

Analytical store, which is a column store, is better suited for such queries because it serializes similar fields of data together and reduces the disk IOPS.

The following image shows transactional row store vs. analytical column store in Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transactional row store Vs analytical column store in Azure Cosmos DB" border="false":::

### Decoupled performance for analytical workloads

There is no impact on the performance of your transactional workloads due to analytical queries, as the analytical store is separate from the transactional store.  Analytical store does not need separate request units (RUs) to be allocated.

### Auto-Sync

Auto-Sync refers to the fully managed capability of Azure Cosmos DB where the inserts, updates, deletes to operational data are automatically synced from transactional store to analytical store in near real time. Auto-sync latency is usually within 2 minutes. In cases of shared throughput database with a large number of containers, auto-sync latency of individual containers could be higher and take up to 5 minutes. We would like to learn more how this latency fits your scenarios. For that, please reach out to the [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com).

The auto-sync capability along with analytical store provides the following key benefits:

#### Scalability & elasticity

By using horizontal partitioning, Azure Cosmos DB transactional store can elastically scale the storage and throughput without any downtime. Horizontal partitioning in the transactional store provides scalability & elasticity in auto-sync to ensure data is synced to the analytical store in near real time. The data sync happens regardless of the transactional traffic throughput, whether it is 1000 operations/sec or 1 million operations/sec, and  it doesn't impact the provisioned throughput in the transactional store. 

#### <a id="analytical-schema"></a>Automatically handle schema updates

Azure Cosmos DB transactional store is schema-agnostic, and it allows you to iterate on your transactional applications without having to deal with schema or index management. In contrast to this, Azure Cosmos DB analytical store is schematized to optimize for analytical query performance. With the auto-sync capability, Azure Cosmos DB manages the schema inference over the latest updates from the transactional store.  It also manages the schema representation in the analytical store out-of-the-box which, includes handling nested data types.

As your schema evolves, and new properties are added over time, the analytical store automatically presents a unionized schema across all historical schemas in the transactional store.

##### Schema constraints

The following constraints are applicable on the operational data in Azure Cosmos DB when you enable analytical store to automatically infer and represent the schema correctly:

* You can have a maximum of 200 properties at any nesting level in the schema and a maximum nesting depth of 5.
  
  * An item with 201 properties at the top level doesn’t satisfy this constraint and hence it will not be represented in the analytical store.
  * An item with more than five nested levels in the schema also doesn’t satisfy this constraint and hence it will not be represented in the analytical store. For example, the following item doesn't satisfy the requirement:

     `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Property names should be unique when compared case insensitively. For example, the following items do not satisfy this constraint and hence will not be represented in the analytical store:

  `{"Name": "fred"} {"name": "john"}` – "Name" and "name" are the same when compared in a case insensitive manner.

##### Schema representation

There are two modes of schema representation in the analytical store. These modes have tradeoffs between the simplicity of a columnar representation, handling the polymorphic schemas, and simplicity of query experience:

* Well-defined schema representation
* Full fidelity schema representation

> [!NOTE]
> For SQL (Core) API accounts, when analytical store is enabled, the default schema representation in the analytical store is well-defined. Whereas for Azure Cosmos DB API for MongoDB accounts, the default schema representation in the analytical store is a full fidelity schema representation. If you have scenarios requiring a different schema representation than the default offering for each of these APIs, reach out to the [Azure Cosmos DB team](mailto:cosmosdbsynapselink@microsoft.com) to enable it.

**Well-defined schema representation**

The well-defined schema representation creates a simple tabular representation of the schema-agnostic data in the transactional store. The well-defined schema representation has the following considerations:

* A property always has the same type across multiple items.

  * For example, `{"a":123} {"a": "str"}` does not have a well-defined schema because `"a"` is sometimes a string and sometimes a number. In this case, the analytical store registers the data type of `“a”` as the data type of `“a”` in the first-occurring item in the lifetime of the container. Items where the data type of `“a”` differs will not be included in the analytical store.
  
    This condition does not apply for null properties. For example, `{"a":123} {"a":null}` is still well defined.

* Array types must contain a single repeated type.

  * For example, `{"a": ["str",12]}` is not a well-defined schema because the array contains a mix of integer and string types.

> [!NOTE]
> If the Azure Cosmos DB analytical store follows the well-defined schema representation and the specification above is violated by certain items, those items will not be included in the analytical store.

**Full fidelity schema representation**

The full fidelity schema representation is designed to handle the full breadth of polymorphic schemas in the schema-agnostic operational data. In this schema representation, no items are dropped from the analytical store even if the well-defined schema constraints (that is no mixed data type fields nor mixed data type arrays) are violated.

This is achieved by translating the leaf properties of the operational data into the analytical store with distinct columns based on the data type of values in the property. The leaf property names are extended with data types as a suffix in the analytical store schema such that they can be queries without ambiguity.

For example, let’s take the following sample document in the transactional store:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

The leaf property `streetNo` within the nested object `address` will be represented in the analytical store schema as a column `address.object.streetNo.int32`. The datatype is added as a suffix to the column. This way, if another document is added to the transactional store where the value of leaf property `streetNo` is "123" (note it’s a string), the schema of the analytical store automatically evolves without altering the type of a previously written column. A new column added to the analytical store as `address.object.streetNo.string` where this value of "123" is stored.

**Data type to suffix map**

Here is a map of all the property data types and their suffix representations in the analytical store:

|Original data type  |Suffix  |Example  |
|---------|---------|---------|
| Double |	".float64" |	24.99|
| Array	| ".array" |	["a", "b"]|
|Binary	| ".binary"	|0|
|Boolean	| ".bool"	|True|
|Int32	| ".int32"	|123|
|Int64	| ".int64"	|255486129307|
|Null	| ".null"	| null|
|String| 	".string" |	"ABC"|
|Timestamp |	".timestamp" |	Timestamp(0, 0)|
|DateTime	|".date"	| ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId	|".objectId"	| ObjectId("5f3f7b59330ec25c132623a2")|
|Document	|".object" |	{"a": "a"}|

### Cost-effective archival of historical data

Data tiering refers to the separation of data between storage infrastructures optimized for different scenarios. Thereby improving the overall performance and cost-effectiveness of the end-to-end data stack. With analytical store, Azure Cosmos DB now supports automatic tiering of data from the transactional store to analytical store with different data layouts. With analytical store optimized in terms of storage cost compared to the transactional store, allows you to retain much longer horizons of operational data for historical analysis.

After the analytical store is enabled, based on the data retention needs of the transactional workloads, you can configure the 'Transactional Store Time to Live (Transactional TTL)' property to have records automatically deleted from the transactional store after a certain time period. Similarly, the  'Analytical Store Time To Live (Analytical TTL)' allows you to manage the lifecycle of data retained in the analytical store independent from the transactional store. By enabling analytical store and configuring TTL properties, you can seamlessly tier and define the data retention period for the two stores.

### Global Distribution

If you have a globally distributed Azure Cosmos DB account, after you enable analytical store for a container, it will be available in all regions of that account.  Any changes to operational data are globally replicated in all regions. You can run analytical queries effectively against the nearest regional copy of your data in Azure Cosmos DB.

### Security

Authentication with the analytical store is the same as the transactional store for a given database. You can use primary or read-only keys for authentication. You can leverage linked service in Synapse Studio to prevent pasting the Azure Cosmos DB keys in the Spark notebooks. Access to this Linked Service is available to anyone who has access into the workspace.

### Support for multiple Azure Synapse Analytics runtimes

The analytical store is optimized to provide scalability, elasticity, and performance for analytical workloads without any dependency on the compute run-times. The storage technology is self-managed to optimize your analytics workloads without manual efforts.

By decoupling the analytical storage system from the analytical compute system, data in Azure Cosmos DB analytical store can be queried simultaneously from the different analytics runtimes supported by Azure Synapse Analytics. As of today, Synapse Analytics supports Apache Spark and SQL serverless with Azure Cosmos DB analytical store.

> [!NOTE]
> You can only read from analytical store using Synapse Analytics run time. You can write the data back to your transactional store as a serving layer.

## <a id="analytical-store-pricing"></a> Pricing

Analytical store follows a consumption-based pricing model where you are charged for:

* Storage: the volume of the data retained in the analytical store every month including historical data as defined by Analytical TTL.

* Analytical write operations: the fully managed synchronization of operational data updates to the analytical store from the transactional store (auto-sync)

* Analytical read operations: the read operations performed against the analytical store from Synapse Analytics Spark and SQL serverless run times.

> [!NOTE]
> Azure Cosmos DB analytical store is currently available in public preview free of any charges.

Analytical store pricing is separate from the transaction store pricing model. There is no concept of provisioned RUs in the analytical store. See [Azure Cosmos DB pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/), for full details on the pricing model for analytical store.

In order to get a high-level cost estimate to enable analytical store on an Azure Cosmos DB container, you can use the [Azure Cosmos DB Capacity planner](https://cosmos.azure.com/capacitycalculator/) and get an estimate of your analytical storage and write operations costs. Analytical read operations costs depends on the analytics workload characteristics but as a high-level estimate, scan of 1 TB of data in analytical store typically results in 130,000 analytical read operations, and results in a cost of $0.065.

## <a id="analytical-ttl"></a> Analytical Time-to-Live (TTL)

Analytical TTL indicates how long data should be retained in your analytical store, for a container. 

If analytical store is enabled, inserts, updates, deletes to operational data are automatically synced from transactional store to analytical store, irrespective of the transactional TTL configuration. The retention of this operational data in the analytical store can be controlled by the Analytical TTL value at the container level, as specified below:

Analytical TTL on a container is set using the `AnalyticalStoreTimeToLiveInSeconds` property:

* If the value is set to "0", missing (or set to null): the analytical store is disabled and no data is replicated from transactional store to analytical store

* If present and the value is set to "-1": the analytical store retains all historical data, irrespective of the retention of the data in the transactional store. This setting indicates that the analytical store has infinite retention of your operational data

* If present and the value is set to some positive number "n": items will expire from the analytical store "n" seconds after their last modified time in the transactional store. This setting can be leveraged if you want to retain your operational data for a limited period of time in the analytical store, irrespective of the retention of the data in the transactional store

Some points to consider:

*	After the analytical store is enabled with an analytical TTL value, it can be updated to a different valid value later. 
*	While transactional TTL can be set at the container or item level, analytical TTL can only be set at the container level currently.
*	You can achieve longer retention of your operational data in the analytical store by setting analytical TTL >= transactional TTL at the container level.
*	The analytical store can be made to mirror the transactional store by setting analytical TTL = transactional TTL.

When you enable analytical store on a container:

* From the Azure portal, the analytical TTL option is set to the default value of -1. You can change this value to 'n' seconds, by navigating to container settings under Data Explorer. 
 
* From the Azure SDK or PowerShell or CLI, the analytical TTL option can be enabled by setting it to either -1 or 'n'. 

To learn more, see [how to configure analytical TTL on a container](configure-synapse-link.md#create-analytical-ttl).

## Next steps

To learn more, see the following docs:

* [Azure Synapse Link for Azure Cosmos DB](synapse-link.md)

* [Get started with Azure Synapse Link for Azure Cosmos DB](configure-synapse-link.md)

* [Frequently asked questions about Synapse Link for Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Link for Azure Cosmos DB Use cases](synapse-link-use-cases.md)
