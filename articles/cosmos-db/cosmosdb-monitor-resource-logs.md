---
title: Monitor Azure Cosmos DB data by using Azure Diagnostic settings
description: Learn how to use Azure Diagnostic settings to monitor the performance and availability of data stored in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
---

# Monitor Azure Cosmos DB data by using diagnostic settings in Azure

Diagnostic settings in Azure are used to collect resource logs. Azure resource Logs are emitted by a resource and provide rich, frequent data about the operation of that resource. These logs are captured per request and they are also referred as "data plane logs". Some examples of the data plane operations include delete, insert, and readFeed. The content of these logs varies by resource type.

Platform metrics and the Activity logs are collected automatically, whereas you must create a diagnostic setting to collect resource logs or forward them outside of Azure Monitor. You can turn on diagnostic setting for Azure Cosmos accounts by using the following steps:

1. Sign into the [Azure portal](https://portal.azure.com).

1. Navigate to your Azure Cosmos account. Open the **Diagnostic settings** pane, and then select **Add diagnostic setting** option.

1. In the **Diagnostic settings** pane, fill the form with the following details: 

    * **Name**: Enter a name for the logs to create.

    * You can store the logs to **Archive to a storage account**, **Stream to an event hub** or **Send to Log Analytics**

1. When you create a diagnostic setting, you specify which category of logs to collect. The categories of logs supported by Azure Cosmos DB are listed below along with sample log collected by them:

 * **DataPlaneRequests**: Select this option to log back-end requests to all APIs, which include SQL, Graph, MongoDB, Cassandra, and Table API accounts in Azure Cosmos DB. Key properties to note are: `Requestcharge`, `statusCode`, `clientIPaddress`, and `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Select this option to log user-initiated requests from the front end to serve requests to Azure Cosmos DB's API for MongoDB, this log type is not available for other API accounts. MongoDB requests will appear in MongoRequests as well as DataPlaneRequests. Key properties to note are: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Select this option to log the query text that was executed. This log type is available for SQL API accounts only.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Select this option to log the statistics of the partition keys. This is currently represented with the storage size (KB) of the partition keys. See the [Troubleshooting issues by using Azure Diagnostic queries](#diagnostic-queries) section of this article. For example queries that use "PartitionKeyStatistics". The log is emitted against the first three partition keys that occupy most data storage. This log contains data such as subscription ID, region name, database name, collection name, partition key, and storage size in KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: This log reports the aggregated per-second RU/s consumption of partition keys. Currently, Azure Cosmos DB reports partition keys for SQL API accounts only and for point read/write and stored procedure operations. other APIs and operation types are not supported. For other APIs, the partition key column in the diagnostic log table will be empty. This log contains data such as subscription ID, region name, database name, collection name, partition key, operation type, and request charge. See the [Troubleshooting issues by using Azure Diagnostic queries](#diagnostic-queries) section of this article. For example queries that use "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: This log contains details on control plane operations like creating an account, adding or removing a region, updating account replication settings etc. This log type is available for all API types that include SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Requests**: Select this option to collect metric data from Azure Cosmos DB to the destinations in the diagnostic setting. This is the same data collected automatically in Azure Metrics. Collect metric data with resource logs to analyze both kinds of data together and to send metric data outside of Azure Monitor.

For detailed information about how to create a diagnostic setting by using the Azure portal, CLI, or PowerShell, see [Create diagnostic setting to collect platform logs and metrics in Azure](../azure-monitor/platform/diagnostic-settings.md) article.


## <a id="diagnostic-queries"></a> Troubleshoot issues with diagnostics queries

1. How to get the request charges for expensive queries?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. How to find which operations are taking most of RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. How to get the distribution for different operations?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. What is the maximum throughput that a partition has consumed?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. How to get the information about the partition keys RU/s consumption per second?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. How to get the request charge for a specific partition key

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. How to get the top partition keys with most RU/s consumed in a specific period? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. How to get the logs for the partition keys whose storage size is greater than 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. How to get partition Key statistics to evaluate skew across top three partitions for database account?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## Next steps

* [Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitor and debug with metrics in Azure Cosmos DB](use-metrics.md)
