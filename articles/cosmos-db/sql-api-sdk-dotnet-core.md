---
title: 'Azure Cosmos DB: SQL .NET Core API, SDK & resources'
description: Learn all about the SQL .NET Core API and SDK including release dates, retirement dates, and changes made between each version of the Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun


---
# Azure Cosmos DB .NET Core SDK for SQL API: Release notes and resources
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK download**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API documentation**|[.NET API reference documentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Samples**|[.NET code samples](sql-api-dotnet-samples.md)|
|**Get started**|[Get started with the Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Web app tutorial**|[Web application development with Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Current supported framework**|[.NET Standard 1.6 and .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## Release Notes

> [!NOTE]
> If you are using .NET Core, please see the latest version 3.x of the [.NET SDK](sql-api-sdk-dotnet-standard.md), which targets .NET Standard. 

### <a name="2.7.0"/>2.7.0

* Added support for arrays and objects in order by queries
* Handle effective partition key collisions
* Added LINQ support for multiple OrderBy operators with ThenBy operator
* Custom serialization settings are now applied to all upsert and replace operations
* Fixed AysncCache deadlock issue so that it will work with a single-threaded task scheduler

### <a name="2.6.0"/>2.6.0

* Added PortReusePolicy to ConnectionPolicy
* Fixed ntdll!RtlGetVersion TypeLoadException issue when SDK is used in a UWP app

### <a name="2.5.1"/>2.5.1

* SDK’s System.Net.Http version now matches what is defined in the NuGet package.
* Allow write requests to fallback to a different region if the original one fails.
* Add session retry policy for write request.

### <a name="2.4.1"/>2.4.1

* Fixes tracing race condition for queries which caused empty pages

### <a name="2.4.0"/>2.4.0

* Increased decimal precision size for LINQ queries.
* Added new classes CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType and PartitionKeyDefinitionVersion
* Added TimeToLivePropertyPath to DocumentCollection
* Added CompositeIndexes and SpatialIndexes to IndexPolicy
* Added Version to PartitionKeyDefinition
* Added None to PartitionKey

### <a name="2.3.0"/>2.3.0

 * Added IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection and MaxTcpConnectionsPerEndpoint to ConnectionPolicy.
 
### <a name="2.2.3"/>2.2.3

* Diagnostics improvements

### <a name="2.2.2"/>2.2.2

* Added environment variable setting “POCOSerializationOnly”.

* Removed DocumentDB.Spatial.Sql.dll and now included in Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="2.2.1"/>2.2.1

* Improvement in retry logic during failover for StoredProcedure execute calls.

* Made DocumentClientEventSource singleton. 

* Fix GatewayAddressCache timeout not honoring ConnectionPolicy RequestTimeout.

### <a name="2.2.0"/>2.2.0

* For direct/TCP transport diagnostics, added TransportException, an internal exception type of the SDK. When present in exception messages, this type prints additional information for troubleshooting client connectivity problems.

* Added new constructor overload which takes a HttpMessageHandler, a HTTP handler stack to use for sending HttpClient requests (e.g., HttpClientHandler).

* Fix bug where header with null values were not being handled properly.

* Improved collection cache validation.

### <a name="2.1.3"/>2.1.3

* Updated System.Net.Security to 4.3.2.

### <a name="2.1.2"/>2.1.2

* Diagnostic tracing improvements.

### <a name="2.1.1"/>2.1.1

* Added more resilience to Multi-region request transient failures.

### <a name="2.1.0"/>2.1.0

* Added Multi-region write support.
* Cross partition query performance improvements with TOP and MaxBufferedItemCount.

### <a name="2.0.0"/>2.0.0

* Added request cancellation support.
* Added SetCurrentLocation to ConnectionPolicy, which automatically populates the preferred locations based on the region.
* Fixed Bug in Cross Partition Queries with Min/Max and a filter that matches no documents on an individual partition.
* DocumentClient methods now have parity with IDocumentClient.
* Updated direct TCP transport stack to reduce the number of connections established.
* Added support for Direct Mode TCP for non-Windows clients.

### <a name="2.0.0-preview2"/>2.0.0-preview2

* Added request cancellation support.
* Added SetCurrentLocation to ConnectionPolicy, which automatically populates the preferred locations based on the region.
* Fixed Bug in Cross Partition Queries with Min/Max and a filter that matches no documents on an individual partition.

### <a name="2.0.0-preview"/>2.0.0-preview

* DocumentClient methods now have parity with IDocumentClient.
* Updated direct TCP transport stack to reduce the number of connections established.
* Added support for Direct Mode TCP for non-Windows clients.

### <a name="1.10.0"/>1.10.0

* Added ConsistencyLevel Property to FeedOptions.
* Added JsonSerializerSettings to RequestOptions and FeedOptions.
* Added EnableReadRequestsFallback to ConnectionPolicy.

### <a name="1.9.1"/>1.9.1

* Fixed KeyNotFoundException for cross partition order by queries in corner cases.
* Fixed bug where JsonProperty attribute in select clause for LINQ queries was not being honored.

### <a name="1.8.2"/>1.8.2

* Fixed bug that is hit under certain race conditions, that results in intermittent "Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token" errors when using Session consistency level.

### <a name="1.8.1"/>1.8.1

* Fixed regression where FeedOptions.MaxItemCount = -1 threw an System.ArithmeticException: page size is negative.
* Added a new ToString() function to QueryMetrics.
* Exposed partition statistics on reading collections.
* Added PartitionKey property to ChangeFeedOptions.
* Minor bug fixes.

### <a name="1.7.1"/>1.7.1
 
 * Adds the ability to specify unique indexes for the documents by using UniqueKeyPolicy property on the DocumentCollection.
 * Fixed a bug in which the custom JsonSerializer settings were not being honored for some queries and stored procedure execution.

### <a name="1.7.0"/>1.7.0
 
 * Branding change from Azure DocumentDB to Azure Cosmos DB in the API Reference documentation, metadata information in assemblies, and the NuGet package.
 * Expose diagnostic information and latency from the response of requests sent with direct connectivity mode. The property names are RequestDiagnosticsString and RequestLatency on ResourceResponse class.
 * This SDK version requires the latest version of Azure Cosmos DB Emulator available for download from https://aka.ms/cosmosdb-emulator.
 
### <a name="1.6.0"/>1.6.0

* Added several reliability fixes and improvements.

### <a name="1.5.1"/>1.5.1 

* Internal changes related to supporting [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="1.5.0"/>1.5.0 

* Added support for PartitionKeyRangeId as a FeedOption for scoping query results to a specific partition key range value.
* Added support for StartTime as a ChangeFeedOption to start looking for the changes after that time.

### <a name="1.4.1"/>1.4.1

*	Fixed an issue in the JsonSerializable class that may cause a stack overflow exception.

### <a name="1.4.0"/>1.4.0

*	Added support for specifying custom JsonSerializerSettings while instantiating a [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instance.

### <a name="1.3.2"/>1.3.2

*	Supporting .NET Standard 1.5 as one of the target frameworks.

### <a name="1.3.1"/>1.3.1

*	Fixed an issue that affected x64 machines that don’t support SSE4 instruction and throw SEHException when running Azure Cosmos DB queries.

### <a name="1.3.0"/>1.3.0

*	Added support for a new consistency level called ConsistentPrefix.
*	Added support for query metrics for individual partitions.
*	Added support for limiting the size of the continuation token for queries.
*	Added support for more detailed tracing for failed requests.
*	Made some performance improvements in the SDK.

### <a name="1.2.2"/>1.2.2

* Fixed an issue that ignored the PartitionKey value provided in FeedOptions for aggregate queries.
* Fixed an issue in transparent handling of partition management during mid-flight cross-partition Order By query execution.

### <a name="1.2.1"/>1.2.1

* Fixed an issue which caused deadlocks in some of the async APIs when used inside ASP.NET context.

### <a name="1.2.0"/>1.2.0

* Fixes to make SDK more resilient to automatic failover under certain conditions.

### <a name="1.1.2"/>1.1.2

* Fix for an issue that occasionally causes a WebException: The remote name could not be resolved.
* Added the support for directly reading a typed document by adding new overloads to ReadDocumentAsync API.

### <a name="1.1.1"/>1.1.1

* Added LINQ support for aggregation queries (COUNT, MIN, MAX, SUM, and AVG).
* Fix for a memory leak issue for the ConnectionPolicy object caused by the use of event handler.
* Fix for an issue wherein UpsertAttachmentAsync was not working when ETag was used.
* Fix for an issue wherein cross partition order-by query continuation was not working when sorting on string field.

### <a name="1.1.0"/>1.1.0

* Added support for aggregation queries (COUNT, MIN, MAX, SUM, and AVG). See [Aggregation support](sql-query-aggregates.md).
* Lowered minimum throughput on partitioned collections from 10,100 RU/s to 2500 RU/s.

### <a name="1.0.0"/>1.0.0

The Azure Cosmos DB .NET Core SDK enables you to build fast, cross-platform [ASP.NET Core](https://www.asp.net/core) and [.NET Core](https://www.microsoft.com/net/core#windows) apps to run on Windows, Mac, and Linux. The latest release of the Azure Cosmos DB .NET Core SDK is fully [Xamarin](https://www.xamarin.com) compatible and be used to build applications that target iOS, Android, and Mono (Linux).  

### <a name="0.1.0-preview"/>0.1.0-preview

The Azure Cosmos DB .NET Core Preview SDK enables you to build fast, cross-platform [ASP.NET Core](https://www.asp.net/core) and [.NET Core](https://www.microsoft.com/net/core#windows) apps to run on Windows, Mac, and Linux.

The Azure Cosmos DB .NET Core Preview SDK has feature parity with the latest version of the [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) and supports the following:
* All [connection modes](performance-tips.md#networking): Gateway mode, Direct TCP, and Direct HTTPs.
* All [consistency levels](consistency-levels.md): Strong, Session, Bounded Staleness, and Eventual.
* [Partitioned collections](partition-data.md).
* [Multi-region database accounts and geo-replication](distribute-data-globally.md).

If you have questions related to this SDK, post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb), or file an issue in the [GitHub repository](https://github.com/Azure/azure-documentdb-dotnet/issues).

## Release & Retirement dates
Microsoft provides notification at least **12 months** in advance of retiring an SDK in order to smooth the transition to a newer/supported version.

New features and functionality and optimizations are only added to the current SDK, as such it is recommended that you always upgrade to the latest SDK version as early as possible. 

Any requests to Azure Cosmos DB using a retired SDK are rejected by the service.

> [!WARNING]
> All versions **1.x** of the .NET Core SDK for SQL API will be retired on **August 30, 2020**.
> 
>
<br/>


| Version | Release Date | Retirement Date |
| --- | --- | --- |
| [2.7.0](#2.7.0) |September  23, 2019 |--- |
| [2.6.0](#2.6.0) |August  30, 2019 |--- |
| [2.5.1](#2.5.1) |July  02, 2019 |--- |
| [2.4.1](#2.4.1) |June  20, 2019 |--- |
| [2.4.0](#2.4.0) |May  05, 2019 |--- |
| [2.3.0](#2.3.0) |April  04, 2019 |--- |
| [2.2.3](#2.2.3) |March  11, 2019 |--- |
| [2.2.2](#2.2.2) |February  06, 2019 |--- |
| [2.2.1](#2.2.1) |December 24, 2018 |--- |
| [2.2.0](#2.2.0) |December 07, 2018 |--- |
| [2.1.3](#2.1.3) |October 15, 2018 |--- |
| [2.1.2](#2.1.2) |October 04, 2018 |--- |
| [2.1.1](#2.1.1) |September 27, 2018 |--- |
| [2.1.0](#2.1.0) |September 21, 2018 |--- |
| [2.0.0](#2.0.0) |September 07, 2018 |--- |
| [1.9.1](#1.9.1) |March 09, 2018 |August 30, 2020 |
| [1.8.2](#1.8.2) |February 21, 2018 |August 30, 2020 |
| [1.8.1](#1.8.1) |February 05, 2018 |August 30, 2020 |
| [1.7.1](#1.7.1) |November 16, 2017 |August 30, 2020 |
| [1.7.0](#1.7.0) |November 10, 2017 |August 30, 2020 |
| [1.6.0](#1.6.0) |October 17, 2017 |August 30, 2020 |
| [1.5.1](#1.5.1) |October 02, 2017 |August 30, 2020 |
| [1.5.0](#1.5.0) |August 10, 2017 |August 30, 2020 | 
| [1.4.1](#1.4.1) |August 07, 2017 |August 30, 2020 |
| [1.4.0](#1.4.0) |August 02, 2017 |August 30, 2020 |
| [1.3.2](#1.3.2) |June 12, 2017 |August 30, 2020 |
| [1.3.1](#1.3.1) |May 23, 2017 |August 30, 2020 |
| [1.3.0](#1.3.0) |May 10, 2017 |August 30, 2020 |
| [1.2.2](#1.2.2) |April 19, 2017 |August 30, 2020 |
| [1.2.1](#1.2.1) |March 29, 2017 |August 30, 2020 |
| [1.2.0](#1.2.0) |March 25, 2017 |August 30, 2020 |
| [1.1.2](#1.1.2) |March 20, 2017 |August 30, 2020 |
| [1.1.1](#1.1.1) |March 14, 2017 |August 30, 2020 |
| [1.1.0](#1.1.0) |February 16, 2017 |August 30, 2020 |
| [1.0.0](#1.0.0) |December 21, 2016 |August 30, 2020 |
| [0.1.0-preview](#0.1.0-preview) |November 15, 2016 |December 31, 2016 |

## See Also
To learn more about Cosmos DB, see [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service page.

