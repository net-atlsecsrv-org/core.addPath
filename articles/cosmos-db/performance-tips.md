---
title: Azure Cosmos DB performance tips for .NET SDK v2
description: Learn client configuration options to improve Azure Cosmos DB .NET v2 SDK performance.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2

---

# Performance tips for Azure Cosmos DB and .NET SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB is a fast and flexible distributed database that scales seamlessly with guaranteed latency and throughput. You don't have to make major architecture changes or write complex code to scale your database with Azure Cosmos DB. Scaling up and down is as easy as making a single API call. To learn more, see [how to provision container throughput](how-to-provision-container-throughput.md) or [how to provision database throughput](how-to-provision-database-throughput.md). But because Azure Cosmos DB is accessed via network calls, there are client-side optimizations you can make to achieve peak performance when you use the [SQL .NET SDK](sql-api-sdk-dotnet-standard.md).

So, if you're trying to improve your database performance, consider these options:

## Upgrade to the .NET V3 SDK

The [.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) is released. If you use the .NET v3 SDK, see the [.NET v3 performance guide](performance-tips-dotnet-sdk-v3-sql.md) for the following information:

- Defaults to Direct TCP mode
- Stream API support
- Support custom serializer to allow System.Text.JSON usage
- Integrated batch and bulk support

## Hosting recommendations

**For query-intensive workloads, use Windows 64-bit instead of Linux or Windows 32-bit host processing**

We recommend Windows 64-bit host processing for improved performance. The SQL SDK includes a native ServiceInterop.dll to parse and optimize queries locally. ServiceInterop.dll is supported only on the Windows x64 platform. For Linux and other unsupported platforms where ServiceInterop.dll isn't available, an additional network call is made to the gateway to get the optimized query. The following types of applications use 32-bit host processing by default. To change host processing to 64-bit processing, follow these steps, based on the type of your application:

- For executable applications, you can change host processing by setting the [platform target](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) to **x64**  in the **Project Properties** window, on the **Build** tab.

- For VSTest-based test projects, you can change host processing by selecting **Test** > **Test Settings** > **Default Processor Architecture as X64** on the Visual Studio **Test** menu.

- For locally deployed ASP.NET web applications, you can change host processing by selecting **Use the 64-bit version of IIS Express for web sites and projects** under **Tools** > **Options** > **Projects and Solutions** > **Web Projects**.

- For ASP.NET web applications deployed on Azure, you can change host processing by selecting the **64-bit** platform in **Application settings** in the Azure portal.

> [!NOTE] 
> By default, new Visual Studio projects are set to **Any CPU**. We recommend that you set your project to **x64** so it doesn't switch to **x86**. A project set to **Any CPU** can easily switch to **x86** if an x86-only dependency is added.<br/>
> ServiceInterop.dll needs to be in the folder that the SDK DLL is being executed from. This should be a concern only if you manually copy DLLs or have custom build/deployment systems.
    
**Turn on server-side garbage collection (GC)**

Reducing the frequency of garbage collection can help in some cases. In .NET, set [gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) to `true`.

**Scale out your client workload**

If you're testing at high throughput levels (more than 50,000 RU/s), the client application could become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

> [!NOTE] 
> High CPU usage can cause increased latency and request timeout exceptions.

## <a id="networking"></a> Networking

**Connection policy: Use direct connection mode**

.NET V2 SDK default connection mode is gateway. You configure the connection mode during the construction of the `DocumentClient` instance by using the `ConnectionPolicy` parameter. If you use direct mode, you need to also set the `Protocol` by using the `ConnectionPolicy` parameter. To learn more about different connectivity options, see the [connectivity modes](sql-sdk-connection-modes.md) article.

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**Ephemeral port exhaustion**

If you see a high connection volume or high port usage on your instances, first verify that your client instances are singletons. In other words, the client instances should be unique for the lifetime of the application.

When running on the TCP protocol, the client optimizes for latency by using the long-lived connections as opposed to the HTTPS protocol, which terminates the connections after 2 minutes of inactivity.

In scenarios where you have sparse access and if you notice a higher connection count when compared to the gateway mode access, you can:

* Configure the [ConnectionPolicy.PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) property to `PrivatePortPool` (effective with framework version>= 4.6.1 and .NET core version >= 2.0): This property allows the SDK to use a small pool of ephemeral ports for different Azure Cosmos DB destination endpoints.
* Configure the [ConnectionPolicy.IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) property must be greater than or equal to 10 minutes. The recommended values are between 20 minutes and 24 hours.

**Call OpenAsync to avoid startup latency on first request**

By default, the first request has higher latency because it needs to fetch the address routing table. When you use [SDK V2](sql-api-sdk-dotnet.md), call `OpenAsync()` once during initialization to avoid this startup latency on the first request. The call looks like: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` will generate requests to obtain the address routing table for all the containers in the account. For accounts that have many containers but whose application accesses a subset of them, `OpenAsync` would generate an unnecessary amount of traffic, which would make the initialization slow. So using `OpenAsync` might not be useful in this scenario because it slows down application startup.

**For performance, collocate clients in same Azure region**

When possible, place any applications that call Azure Cosmos DB in the same region as the Azure Cosmos DB database. Here's an approximate comparison: calls to Azure Cosmos DB within the same region complete within 1 ms to 2 ms, but the latency between the West and East coast of the US is more than 50 ms. This latency can vary from request to request, depending on the route taken by the request as it passes from the client to the Azure datacenter boundary. You can get the lowest possible latency by ensuring the calling application is located within the same Azure region as the provisioned Azure Cosmos DB endpoint. For a list of available regions, see [Azure regions](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="The Azure Cosmos DB connection policy" border="false":::

**Increase the number of threads/tasks**
<a id="increase-threads"></a>

Because calls to Azure Cosmos DB are made over the network, you might need to vary the degree of parallelism of your requests so that the client application spends minimal time waiting between requests. For example, if you're using the .NET [Task Parallel Library](/dotnet/standard/parallel-programming/task-parallel-library-tpl), create on the order of hundreds of tasks that read from or write to Azure Cosmos DB.

**Enable accelerated networking**
 
To reduce latency and CPU jitter, we recommend that you enable accelerated networking on client virtual machines. See [Create a Windows virtual machine with accelerated networking](../virtual-network/create-vm-accelerated-networking-powershell.md) or [Create a Linux virtual machine with accelerated networking](../virtual-network/create-vm-accelerated-networking-cli.md).

## SDK usage

**Install the most recent SDK**

The Azure Cosmos DB SDKs are constantly being improved to provide the best performance. See the [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) pages to determine the most recent SDK and review improvements.

**Use a singleton Azure Cosmos DB client for the lifetime of your application**

Each `DocumentClient` instance is thread-safe and performs efficient connection management and address caching when operating in direct mode. To allow efficient connection management and better SDK client performance, we recommend that you use a single instance per `AppDomain` for the lifetime of the application.

**Increase System.Net MaxConnections per host when using gateway mode**

Azure Cosmos DB requests are made over HTTPS/REST when you use gateway mode. They're subjected to the default connection limit per hostname or IP address. You might need to set `MaxConnections` to a higher value (100 to 1,000) so the client library can use multiple simultaneous connections to Azure Cosmos DB. In .NET SDK 1.8.0 and later, the default value for [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) is 50. To change the value, you can set [Documents.Client.ConnectionPolicy.MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) to a higher value.

**Tune parallel queries for partitioned collections**

SQL .NET SDK 1.9.0 and later support parallel queries, which enable you to query a partitioned collection in parallel. For more information, see [code samples](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) related to working with the SDKs. Parallel queries are designed to provide better query latency and throughput than their serial counterpart. Parallel queries provide two parameters that you can tune to fit your requirements: 
- `MaxDegreeOfParallelism` controls the maximum number of partitions that can be queried in parallel. 
- `MaxBufferedItemCount` controls the number of pre-fetched results.

***Tuning degree of parallelism***

Parallel query works by querying multiple partitions in parallel. But data from an individual partition is fetched serially with respect to the query. Setting `MaxDegreeOfParallelism` in [SDK V2](sql-api-sdk-dotnet.md) to the number of partitions has the best chance of achieving the most performant query, provided all other system conditions remain the same. If you don't know the number of partitions, you can set the degree of parallelism to a high number. The system will choose the minimum (number of partitions, user provided input) as the degree of parallelism.

Parallel queries produce the most benefit if the data is evenly distributed across all partitions with respect to the query. If the partitioned collection is partitioned so that all or most of the data returned by a query is concentrated in a few partitions (one partition is the worst case), those partitions will bottleneck the performance of the query.

***Tuning MaxBufferedItemCount***
    
Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. This pre-fetching helps improve the overall latency of a query. The `MaxBufferedItemCount` parameter limits the number of pre-fetched results. Set `MaxBufferedItemCount` to the expected number of results returned (or a higher number) to allow the query to receive the maximum benefit from pre-fetching.

Pre-fetching works the same way regardless of the degree of parallelism, and there's a single buffer for the data from all partitions.  

**Implement backoff at RetryAfter intervals**

During performance testing, you should increase load until a small rate of requests are throttled. If requests are throttled, the client application should back off on throttle for the server-specified retry interval. Respecting the backoff ensures you spend a minimal amount of time waiting between retries. 

Retry policy support is included in these SDKs:
- Version 1.8.0 and later of the [.NET SDK for SQL](sql-api-sdk-dotnet.md) and the [Java SDK for SQL](sql-api-sdk-java.md)
- Version 1.9.0 and later of the [Node.js SDK for SQL](sql-api-sdk-node.md) and the [Python SDK for SQL](sql-api-sdk-python.md)
- All supported versions of the [.NET Core](sql-api-sdk-dotnet-core.md) SDKs 

For more information, see [RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter).
    
In version 1.19 and later of the .NET SDK, there's a mechanism for logging additional diagnostic information and troubleshooting latency issues, as shown in the following sample. You can log the diagnostic string for requests that have a higher read latency. The captured diagnostic string will help you understand how many times you received 429 errors for a given request.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Cache document URIs for lower read latency**

Cache document URIs whenever possible for the best read performance. You need to define logic to cache the resource ID when you create a resource. Lookups based on resource IDs are faster than name-based lookups, so caching these values improves performance.

**Tune the page size for queries/read feeds for better performance**

When you do a bulk read of documents by using read feed functionality (for example, `ReadDocumentFeedAsync`) or when you issue a SQL query, the results are returned in a segmented fashion if the result set is too large. By default, results are returned in chunks of 100 items or 1 MB, whichever limit is hit first.

To reduce the number of network round trips required to retrieve all applicable results, you can increase the page size by using [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) to request as many as 1,000 headers. When you need to display only a few results, for example, if your user interface or application API returns only 10 results at a time, you can also decrease the page size to 10 to reduce the throughput consumed for reads and queries.

> [!NOTE] 
> The `maxItemCount` property shouldn't be used just for pagination. Its main use is to improve the performance of queries by reducing the maximum number of items returned in a single page.  

You can also set the page size by using the available Azure Cosmos DB SDKs. The [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet&preserve-view=true) property in `FeedOptions` allows you to set the maximum number of items to be returned in the enumeration operation. When `maxItemCount` is set to -1, the SDK automatically finds the optimal value, depending on the document size. For example:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
When a query is executed, the resulting data is sent within a TCP packet. If you specify too low a value for `maxItemCount`, the number of trips required to send the data within the TCP packet is high, which affects performance. So if you're not sure what value to set for the `maxItemCount` property, it's best to set it to -1 and let the SDK choose the default value.

**Increase the number of threads/tasks**

See [Increase the number of threads/tasks](#increase-threads) in the networking section of this article.

## Indexing policy
 
**Exclude unused paths from indexing for faster writes**

The Azure Cosmos DB indexing policy also allows you to specify which document paths to include in or exclude from indexing by using indexing paths (IndexingPolicy.IncludedPaths and IndexingPolicy.ExcludedPaths). Indexing paths can improve write performance and reduce index storage for scenarios in which the query patterns are known beforehand. This is because indexing costs correlate directly to the number of unique paths indexed. For example, this code shows how to exclude an entire section of the documents (a subtree) from indexing by using the "*" wildcard:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

For more information, see [Azure Cosmos DB indexing policies](index-policy.md).

## <a id="measure-rus"></a> Throughput

**Measure and tune for lower Request Units/second usage**

Azure Cosmos DB offers a rich set of database operations. These operations include relational and hierarchical queries with UDFs, stored procedures, and triggers, all operating on the documents within a database collection. The cost associated with each of these operations varies depending on the CPU, IO, and memory required to complete the operation. Instead of thinking about and managing hardware resources, you can think of a Request Unit (RU) as a single measure for the resources required to perform various database operations and service an application request.

Throughput is provisioned based on the number of [Request Units](request-units.md) set for each container. Request Unit consumption is evaluated as a rate per second. Applications that exceed the provisioned Request Unit rate for their container are limited until the rate drops below the provisioned level for the container. If your application requires a higher level of throughput, you can increase your throughput by provisioning additional Request Units.

The complexity of a query affects how many Request Units are consumed for an operation. The number of predicates, the nature of the predicates, the number of UDFs, and the size of the source dataset all influence the cost of query operations.

To measure the overhead of any operation (create, update, or delete), inspect the [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) header (or the equivalent `RequestCharge` property in `ResourceResponse\<T>` or `FeedResponse\<T>` in the .NET SDK) to measure the number of Request Units consumed by the operations:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

The request charge returned in this header is a fraction of your provisioned throughput (that is, 2,000 RUs / second). For example, if the preceding query returns 1,000 1-KB documents, the cost of the operation is 1,000. So, within one second, the server honors only two such requests before rate limiting later requests. For more information, see [Request Units](request-units.md) and the [Request Unit calculator](https://www.documentdb.com/capacityplanner).
<a id="429"></a>

**Handle rate limiting/request rate too large**

When a client attempts to exceed the reserved throughput for an account, there's no performance degradation at the server and no use of throughput capacity beyond the reserved level. The server will preemptively end the request with RequestRateTooLarge (HTTP status code 429). It will return an [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) header that indicates the amount of time, in milliseconds, that the user must wait before attempting the request again.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

The SDKs all implicitly catch this response, respect the server-specified retry-after header, and retry the request. Unless your account is being accessed concurrently by multiple clients, the next retry will succeed.

If you have more than one client cumulatively operating consistently above the request rate, the default retry count currently set to 9 internally by the client might not suffice. In this case, the client throws a DocumentClientException with status code 429 to the application. 

You can change the default retry count by setting the `RetryOptions` on the `ConnectionPolicy` instance. By default, the DocumentClientException with status code 429 is returned after a cumulative wait time of 30 seconds if the request continues to operate above the request rate. This error returns even when the current retry count is less than the maximum retry count, whether the current value is the default of 9 or a user-defined value.

The automated retry behavior helps improve resiliency and usability for most applications. But it might not be the best behavior when you're doing performance benchmarks, especially when you're measuring latency. The client-observed latency will spike if the experiment hits the server throttle and causes the client SDK to silently retry. To avoid latency spikes during performance experiments, measure the charge returned by each operation and ensure that requests are operating below the reserved request rate. For more information, see [Request Units](request-units.md).

**For higher throughput, design for smaller documents**

The request charge (that is, the request-processing cost) of a given operation correlates directly to the size of the document. Operations on large documents cost more than operations on small documents.

## Next steps

For a sample application that's used to evaluate Azure Cosmos DB for high-performance scenarios on a few client machines, see [Performance and scale testing with Azure Cosmos DB](performance-testing.md).

To learn more about designing your application for scale and high performance, see [Partitioning and scaling in Azure Cosmos DB](partitioning-overview.md).