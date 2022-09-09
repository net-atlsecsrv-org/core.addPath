---
title: Understand outputs from Azure Stream Analytics
description: This article describes data output options available in Azure Stream Analytics, including Power BI for analysis results.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
---

# Understand outputs from Azure Stream Analytics
This article describes the types of outputs available for an Azure Stream Analytics job. Outputs let you store and save the results of the Stream Analytics job. By using the output data, you can do further business analytics and data warehousing of your data.

When you design your Stream Analytics query, refer to the name of the output by using the [INTO clause](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). You can use a single output per job, or multiple outputs per streaming job (if you need them) by providing multiple INTO clauses in the query.

To create, edit, and test Stream Analytics job outputs, you can use the [Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), and [Visual Studio](stream-analytics-quick-create-vs.md).

Some outputs types support [partitioning](#partitioning). [Output batch sizes](#output-batch-size) vary to optimize throughput.


## Azure Data Lake Store
Stream Analytics supports [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store is an enterprise-wide, hyperscale repository for big data analytic workloads. You can use Data Lake Store to store data of any size, type, and ingestion speed for operational and exploratory analytics. Stream Analytics has to be authorized to access Data Lake Store.

Azure Data Lake Store output from Stream Analytics is currently not available in the Azure China (21Vianet) and Azure Germany (T-Systems International) regions.

### Authorize an Azure Data Lake Store account

1. When you select Data Lake Store as an output in the Azure portal, you're prompted to authorize a connection to an existing Data Lake Store instance.

   ![Authorize a connection to Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. If you already have access to Data Lake Store, select **Authorize Now**. A page pops up and indicates **Redirecting to authorization**. After authorization succeeds, you're presented with the page that allows you to configure the Data Lake Store output.

3. After you have the Data Lake Store account authenticated, you can configure the properties for your Data Lake Store output.

   ![Define Data Lake Store as Stream Analytics output](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

The following table lists property names and their descriptions to configure your Data Lake Store output.   

| Property name | Description |
| --- | --- |
| Output alias | A friendly name used in queries to direct the query output to Data Lake Store. |
| Account name | The name of the Data Lake Store account where you're sending your output. You're presented with a drop-down list of Data Lake Store accounts that are available in your subscription. |
| Path prefix pattern | The file path that's used to write your files within the specified Data Lake Store account. You can specify one or more instances of the {date} and {time} variables:<br /><ul><li>Example 1: folder1/logs/{date}/{time}</li><li>Example 2: folder1/logs/{date}</li></ul><br />The time stamp of the created folder structure follows UTC and not local time.<br /><br />If the file path pattern doesn't contain a trailing slash (/), the last pattern in the file path is treated as a file name prefix. <br /><br />New files are created in these circumstances:<ul><li>Change in output schema</li><li>External or internal restart of a job</li></ul> |
| Date format | Optional. If the date token is used in the prefix path, you can select the date format in which your files are organized. Example: YYYY/MM/DD |
|Time format | Optional. If the time token is used in the prefix path, specify the time format in which your files are organized. Currently the only supported value is HH. |
| Event serialization format | The serialization format for output data. JSON, CSV, and Avro are supported.|
| Encoding | If you're using CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time.|
| Delimiter | Applicable only for CSV serialization. Stream Analytics supports a number of common delimiters for serializing CSV data. Supported values are comma, semicolon, space, tab, and vertical bar.|
| Format | Applicable only for JSON serialization. **Line separated** specifies that the output is formatted by having each JSON object separated by a new line. **Array** specifies that the output is formatted as an array of JSON objects. This array is closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it's preferable to use line-separated JSON, because it doesn't require any special handling while the output file is still being written to.|

### Renew Data Lake Store authorization
You need to reauthenticate your Data Lake Store account if its password has changed since your job was created or last authenticated. If you don't reauthenticate, your job does not produce output results and shows an error that indicates the need for reauthorization in the Operation Logs. 

Currently, the authentication token needs to be manually refreshed every 90 days for all jobs with Data Lake Store output. You can overcome this limitation by [authenticating through managed identities (preview)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

To renew authorization:

1. Select **Stop** to stop your job.
1. Go to your Data Lake Store output and select the **Renew authorization** link.

   For a brief time, a pop-up page indicates **Redirecting to authorization**. If authorization is successful, the page indicates **Authorization has been successfully renewed** and then closes automatically. 
   
1. Select **Save** at the bottom of the page. You can then restart your job from the **Last Stopped Time** to avoid data loss.

![Renew Data Lake Store authorization in output](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## SQL Database
You can use [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) as an output for data that's relational in nature or for applications that depend on content being hosted in a relational database. Stream Analytics jobs write to an existing table in SQL Database. The table schema must exactly match the fields and their types in your job's output. You can also specify [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) as an output via the SQL Database output option. To learn about ways to improve write throughput, see the [Stream Analytics with Azure SQL Database as output](stream-analytics-sql-output-perf.md) article. 

The following table lists the property names and their description for creating a SQL Database output.

| Property name | Description |
| --- | --- |
| Output alias |A friendly name used in queries to direct the query output to this database. |
| Database | The name of the database where you're sending your output. |
| Server name | The SQL Database server name. |
| Username | The username that has write access to the database. Stream Analytics supports only SQL authentication. |
| Password | The password to connect to the database. |
| Table | The table name where the output is written. The table name is case-sensitive. The schema of this table should exactly match the number of fields and their types that your job output generates. |
|Inherit partition scheme| An option for inheriting the partitioning scheme of your previous query step, to enable fully parallel topology with multiple writers to the table. For more information, see [Azure Stream Analytics output to Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Match batch count| The recommended limit on the number of records sent with every bulk insert transaction.|

> [!NOTE]
> Currently the Azure SQL Database offering is supported for a job output in Stream Analytics. An Azure virtual machine running SQL Server with a database attached is not supported. This is subject to change in future releases.
>

## Blob storage
Azure Blob storage offers a cost-effective and scalable solution for storing large amounts of unstructured data in the cloud. For an introduction on Blob storage and its usage, see [How to use blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

The following table lists the property names and their descriptions for creating a blob output.

| Property name       | Description                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Output alias        | A friendly name used in queries to direct the query output to this blob storage. |
| Storage account     | The name of the storage account where you're sending your output.               |
| Storage account key | The secret key associated with the storage account.                              |
| Storage container   | A logical grouping for blobs stored in the Azure Blob service. When you upload a blob to the Blob service, you must specify a container for that blob. |
| Path pattern | Optional. The file path pattern that's used to write your blobs within the specified container. <br /><br /> In the path pattern, you can choose to use one or more instances of the date and time variables to specify the frequency that blobs are written: <br /> {date}, {time} <br /><br />You can use custom blob partitioning to specify one custom {field} name from your event data to partition blobs. The field name is alphanumeric and can include spaces, hyphens, and underscores. Restrictions on custom fields include the following: <ul><li>Field names aren't case-sensitive. For example, the service can't differentiate between column "ID" and column "id."</li><li>Nested fields are not permitted. Instead, use an alias in the job query to "flatten" the field.</li><li>Expressions can't be used as a field name.</li></ul> <br />This feature enables the use of custom date/time format specifier configurations in the path. Custom date and time formats must be specified one at a time, enclosed by the {datetime:\<specifier>} keyword. Allowable inputs for \<specifier> are yyyy, MM, M, dd, d, HH, H, mm, m, ss, or s. The {datetime:\<specifier>} keyword can be used multiple times in the path to form custom date/time configurations. <br /><br />Examples: <ul><li>Example 1: cluster1/logs/{date}/{time}</li><li>Example 2: cluster1/logs/{date}</li><li>Example 3: cluster1/{client_id}/{date}/{time}</li><li>Example 4: cluster1/{datetime:ss}/{myField} where the query is: SELECT data.myField AS myField FROM Input;</li><li>Example 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />The time stamp of the created folder structure follows UTC and not local time.<br /><br />File naming uses the following convention: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Example output files:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />For more information about this feature, see [Azure Stream Analytics custom blob output partitioning](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Date format | Optional. If the date token is used in the prefix path, you can select the date format in which your files are organized. Example: YYYY/MM/DD |
| Time format | Optional. If the time token is used in the prefix path, specify the time format in which your files are organized. Currently the only supported value is HH. |
| Event serialization format | Serialization format for output data. JSON, CSV, and Avro are supported. |
| Encoding    | If you're using CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time. |
| Delimiter   | Applicable only for CSV serialization. Stream Analytics supports a number of common delimiters for serializing CSV data. Supported values are comma, semicolon, space, tab, and vertical bar. |
| Format      | Applicable only for JSON serialization. **Line separated** specifies that the output is formatted by having each JSON object separated by a new line. **Array** specifies that the output is formatted as an array of JSON objects. This array is closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it's preferable to use line-separated JSON, because it doesn't require any special handling while the output file is still being written to. |

When you're using Blob storage as output, a new file is created in the blob in the following cases:

* If the file exceeds the maximum number of allowed blocks (currently 50,000). You might reach the maximum allowed number of blocks without reaching the maximum allowed blob size. For example, if the output rate is high, you can see more bytes per block, and the file size is larger. If the output rate is low, each block has less data, and the file size is smaller.
* If there's a schema change in the output, and the output format requires fixed schema (CSV and Avro).
* If a job is restarted, either externally by a user stopping it and starting it, or internally for system maintenance or error recovery.
* If the query is fully partitioned, and a new file is created for each output partition.
* If the user deletes a file or a container of the storage account.
* If the output is time partitioned by using the path prefix pattern, and a new blob is used when the query moves to the next hour.
* If the output is partitioned by a custom field, and a new blob is created per partition key if it does not exist.
* If the output is partitioned by a custom field where the partition key cardinality exceeds 8,000, and a new blob is created per partition key.

## Event Hubs
The [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service is a highly scalable publish-subscribe event ingestor. It can collect millions of events per second. One use of an event hub as output is when the output of a Stream Analytics job becomes the input of another streaming job.

You need a few parameters to configure data streams from event hubs as an output.

| Property name | Description |
| --- | --- |
| Output alias | A friendly name used in queries to direct the query output to this event hub. |
| Event hub namespace |A container for a set of messaging entities. When you created a new event hub, you also created an event hub namespace. |
| Event hub name | The name of your event hub output. |
| Event hub policy name | The shared access policy, which you can create on the event hub's **Configure** tab. Each shared access policy has a name, permissions that you set, and access keys. |
| Event hub policy key | The shared access key that's used to authenticate access to the event hub namespace. |
| Partition key column | Optional. A column that contains the partition key for event hub output. |
| Event serialization format | The serialization format for output data. JSON, CSV, and Avro are supported. |
| Encoding | For CSV and JSON, UTF-8 is the only supported encoding format at this time. |
| Delimiter | Applicable only for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab, and vertical bar. |
| Format | Applicable only for JSON serialization. **Line separated** specifies that the output is formatted by having each JSON object separated by a new line. **Array** specifies that the output is formatted as an array of JSON objects. This array is closed only when the job stops or Stream Analytics has moved on to the next time window. In general, it's preferable to use line-separated JSON, because it doesn't require any special handling while the output file is still being written to. |
| Property columns | Optional. Comma-separated columns that need to be attached as user properties of the outgoing message instead of the payload. More info about this feature is in the section [Custom metadata properties for output](#custom-metadata-properties-for-output). |

## Power BI
You can use [Power BI](https://powerbi.microsoft.com/) as an output for a Stream Analytics job to provide for a rich visualization experience of analysis results. You can use this capability for operational dashboards, report generation, and metric-driven reporting.

Power BI output from Stream Analytics is currently not available in the Azure China (21Vianet) and Azure Germany (T-Systems International) regions.

### Authorize a Power BI account
1. When Power BI is selected as an output in the Azure portal, you're prompted to authorize an existing Power BI user or to create a new Power BI account.
   
   ![Authorize a Power BI user to configure output](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Create a new account if you don’t yet have one, and then select **Authorize Now**. The following page appears:
   
   ![Authenticate to Power BI from Azure account](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Provide the work or school account for authorizing the Power BI output. If you're not already signed up for Power BI, select **Sign up now**. The work or school account that you use for Power BI might be different from the Azure subscription account that you're now signed in with.

### Configure the Power BI output properties
After you have the Power BI account authenticated, you can configure the properties for your Power BI output. The following table lists property names and their descriptions to configure your Power BI output.

| Property name | Description |
| --- | --- |
| Output alias |Provide a friendly name that's used in queries to direct the query output to this Power BI output. |
| Group workspace |To enable sharing data with other Power BI users, you can select groups inside your Power BI account or choose **My Workspace** if you don't want to write to a group. Updating an existing group requires renewing the Power BI authentication. |
| Dataset name |Provide a dataset name that you want the Power BI output to use. |
| Table name |Provide a table name under the dataset of the Power BI output. Currently, Power BI output from Stream Analytics jobs can have only one table in a dataset. |

For a walkthrough of configuring a Power BI output and dashboard, see the [Azure Stream Analytics and Power BI](stream-analytics-power-bi-dashboard.md) article.

> [!NOTE]
> Don't explicitly create the dataset and table in the Power BI dashboard. The dataset and table are automatically populated when the job is started and the job starts pumping output into Power BI. If the job query doesn’t generate any results, the dataset and table aren't created. If Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data is overwritten.
>

### Create a schema
Azure Stream Analytics creates a Power BI dataset and table schema for the user if they don't already exist. In all other cases, the table is updated with new values. Currently, only one table can exist within a dataset. 

Power BI uses the first-in, first-out (FIFO) retention policy. Data will collect in a table until it hits 200,000 rows.

### Convert a data type from Stream Analytics to Power BI
Azure Stream Analytics updates the data model dynamically at runtime if the output schema changes. Column name changes, column type changes, and the addition or removal of columns are all tracked.

This table covers the data type conversions from [Stream Analytics data types](https://msdn.microsoft.com/library/azure/dn835065.aspx) to Power BI [Entity Data Model (EDM) types](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), if a Power BI dataset and table don't exist.

From Stream Analytics | To Power BI
-----|-----
bigint | Int64
nvarchar(max) | String
datetime | Datetime
float | Double
Record array | String type, constant value "IRecord" or "IArray"

### Update the schema
Stream Analytics infers the data model schema based on the first set of events in the output. Later, if necessary, the data model schema is updated to accommodate incoming events that might not fit into the original schema.

Avoid the `SELECT *` query to prevent dynamic schema update across rows. In addition to potential performance implications, it might result in uncertainty of the time taken for the results. Select the exact fields that need to be shown on the Power BI dashboard. Additionally, the data values should be compliant with the chosen data type.


Previous/current | Int64 | String | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datetime | String | String |  Datetime | String


### Renew Power BI Authorization
If your Power BI account password changes after your Stream Analytics job was created or last authenticated, you need to reauthenticate Stream Analytics. If Azure Multi-Factor Authentication is configured on your Azure Active Directory (Azure AD) tenant, you also need to renew Power BI authorization every two weeks. A symptom of this issue is no job output and an "Authenticate user error" in the Operation Logs:

  ![Power BI authenticate user error](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

To resolve this issue, stop your running job and go to your Power BI output. Select the **Renew authorization** link, and restart your job from the **Last Stopped Time** to avoid data loss.

  ![Renew Power BI authorization for output](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Table storage
[Azure Table storage](../storage/common/storage-introduction.md) offers highly available, massively scalable storage, so that an application can automatically scale to meet user demand. Table storage is Microsoft’s NoSQL key/attribute store, which you can use for structured data with fewer constraints on the schema. Azure Table storage can be used to store data for persistence and efficient retrieval.

The following table lists the property names and their descriptions for creating a table output.

| Property name | Description |
| --- | --- |
| Output alias |A friendly name used in queries to direct the query output to this table storage. |
| Storage account |The name of the storage account where you're sending your output. |
| Storage account key |The access key associated with the storage account. |
| Table name |The name of the table. The table gets created if it doesn't exist. |
| Partition key |The name of the output column that contains the partition key. The partition key is a unique identifier for the partition within a table that forms the first part of an entity's primary key. It's a string value that can be up to 1 KB in size. |
| Row key |The name of the output column that contains the row key. The row key is a unique identifier for an entity within a partition. It forms the second part of an entity’s primary key. The row key is a string value that can be up to 1 KB in size. |
| Batch size |The number of records for a batch operation. The default (100) is sufficient for most jobs. See the [Table Batch Operation spec](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) for more details on modifying this setting. |

## Service Bus queues
[Service Bus queues](https://msdn.microsoft.com/library/azure/hh367516.aspx) offer a FIFO message delivery to one or more competing consumers. Typically, messages are received and processed by the receivers in the temporal order in which they were added to the queue. Each message is received and processed by only one message consumer.

The following table lists the property names and their descriptions for creating a queue output.

| Property name | Description |
| --- | --- |
| Output alias |A friendly name used in queries to direct the query output to this Service Bus queue. |
| Service Bus namespace |A container for a set of messaging entities. |
| Queue name |The name of the Service Bus queue. |
| Queue policy name |When you create a queue, you can also create shared access policies on the queue's **Configure** tab. Each shared access policy has a name, permissions that you set, and access keys. |
| Queue policy key |The shared access key that's used to authenticate access to the Service Bus namespace. |
| Event serialization format |The serialization format for output data. JSON, CSV, and Avro are supported. |
| Encoding |For CSV and JSON, UTF-8 is the only supported encoding format at this time. |
| Delimiter |Applicable only for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab, and vertical bar. |
| Format |Applicable only for JSON type. **Line separated** specifies that the output is formatted by having each JSON object separated by a new line. **Array** specifies that the output is formatted as an array of JSON objects. |
| Property columns | Optional. Comma-separated columns that need to be attached as user properties of the outgoing message instead of the payload. More info about this feature is in the section [Custom metadata properties for output](#custom-metadata-properties-for-output). |

The number of partitions is [based on the Service Bus SKU and size](../service-bus-messaging/service-bus-partitioning.md). Partition key is a unique integer value for each partition.

## Service Bus topics
Service Bus queues provide a one-to-one communication method from sender to receiver. [Service Bus topics](https://msdn.microsoft.com/library/azure/hh367516.aspx) provide a one-to-many form of communication.

The following table lists the property names and their descriptions for creating a topic output.

| Property name | Description |
| --- | --- |
| Output alias |A friendly name used in queries to direct the query output to this Service Bus topic. |
| Service Bus namespace |A container for a set of messaging entities. When you created a new event hub, you also created a Service Bus namespace. |
| Topic name |Topics are messaging entities, similar to event hubs and queues. They're designed to collect event streams from devices and services. When a topic is created, it's also given a specific name. The messages sent to a topic aren't available unless a subscription is created, so ensure there's one or more subscriptions under the topic. |
| Topic policy name |When you create a topic, you can also create shared access policies on the topic's **Configure** tab. Each shared access policy has a name, permissions that you set, and access keys. |
| Topic policy key |The shared access key that's used to authenticate access to the Service Bus namespace. |
| Event serialization format |The serialization format for output data. JSON, CSV, and Avro are supported. |
| Encoding |If you're using CSV or JSON format, an encoding must be specified. UTF-8 is the only supported encoding format at this time. |
| Delimiter |Applicable only for CSV serialization. Stream Analytics supports a number of common delimiters for serializing data in CSV format. Supported values are comma, semicolon, space, tab, and vertical bar. |
| Property columns | Optional. Comma-separated columns that need to be attached as user properties of the outgoing message instead of the payload. More info about this feature is in the section [Custom metadata properties for output](#custom-metadata-properties-for-output). |

The number of partitions is [based on the Service Bus SKU and size](../service-bus-messaging/service-bus-partitioning.md). The partition key is a unique integer value for each partition.

## Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) is a globally distributed database service that offers limitless elastic scale around the globe, rich query, and automatic indexing over schema-agnostic data models. To learn about Azure Cosmos DB collection options for Stream Analytics, see the [Stream Analytics with Azure Cosmos DB as output](stream-analytics-documentdb-output.md) article.

Azure Cosmos DB output from Stream Analytics is currently not available in the Azure China (21Vianet) and Azure Germany (T-Systems International) regions.

> [!Note]
> At this time, Azure Stream Analytics only supports connection to Azure Cosmos DB by using the SQL API.
> Other Azure Cosmos DB APIs are not yet supported. If you point Azure Stream Analytics to the Azure Cosmos DB accounts created with other APIs, the data might not be properly stored.

The following table describes the properties for creating an Azure Cosmos DB output.

| Property name | Description |
| --- | --- |
| Output alias | An alias to refer this output in your Stream Analytics query. |
| Sink | Azure Cosmos DB. |
| Import option | Choose either **Select Cosmos DB from your subscription** or **Provide Cosmos DB settings manually**.
| Account ID | The name or endpoint URI of the Azure Cosmos DB account. |
| Account key | The shared access key for the Azure Cosmos DB account. |
| Database | The Azure Cosmos DB database name. |
| Collection name pattern | The collection name or the pattern for the collections to be used. <br />You can construct the collection name format by using the optional {partition} token, where partitions start from 0. Two examples:  <br /><ul><li> _MyCollection_: One collection named "MyCollection" must exist.</li>  <li> _MyCollection{partition}_: Based on the partitioning column.</li></ul> The partitioning column collections must exist: "MyCollection0," "MyCollection1," "MyCollection2," and so on. |
| Partition key | Optional. You need this only if you're using a {partition} token in your collection name pattern.<br /> The partition key is the name of the field in output events that's used to specify the key for partitioning output across collections.<br /> For single collection output, you can use any arbitrary output column. An example is PartitionId. |
| Document ID |Optional. The name of the field in output events that's used to specify the primary key on which insert or update operations are based.

## Azure Functions
Azure Functions is a serverless compute service that you can use to run code on-demand without having to explicitly provision or manage infrastructure. It lets you implement code that's triggered by events occurring in Azure or partner services. This ability of Azure Functions to respond to triggers makes it a natural output for Azure Stream Analytics. This output adapter enables users to connect Stream Analytics to Azure Functions, and run a script or piece of code in response to a variety of events.

Azure Functions output from Stream Analytics is currently not available in the Azure China (21Vianet) and Azure Germany (T-Systems International) regions.

Azure Stream Analytics invokes Azure Functions via HTTP triggers. The Azure Functions output adapter is available with the following configurable properties:

| Property name | Description |
| --- | --- |
| Function app |The name of your Azure Functions app. |
| Function |The name of the function in your Azure Functions app. |
| Key |If you want to use an Azure Function from another subscription, you can do so by providing the key to access your function. |
| Max batch size |A property that lets you set the maximum size for each output batch that's sent to your Azure function. The input unit is in bytes. By default, this value is 262,144 bytes (256 KB). |
| Max batch count  |A property that lets you specify the maximum number of events in each batch that's sent to Azure Functions. The default value is 100. |

When Azure Stream Analytics receives a 413 ("http Request Entity Too Large") exception from an Azure function, it reduces the size of the batches that it sends to Azure Functions. In your Azure function code, use this exception to make sure that Azure Stream Analytics doesn’t send oversized batches. Also, make sure that the maximum batch count and size values used in the function are consistent with the values entered in the Stream Analytics portal.

Also, in a situation where there's no event landing in a time window, no output is generated. As a result, the **computeResult** function isn't called. This behavior is consistent with the built-in windowed aggregate functions.

## Custom metadata properties for output 

You can attach query columns as user properties to your outgoing messages. These columns don't go into the payload. The properties are present in the form of a dictionary on the output message. *Key* is the column name and *value* is the column value in the properties dictionary. All Stream Analytics data types are supported except Record and Array.  

Supported outputs: 
* Service Bus queue 
* Service Bus topic 
* Event hub 

In the following example, we add the two fields `DeviceId` and `DeviceStatus` to the metadata. 
* Query: `select *, DeviceId, DeviceStatus from iotHubInput`
* Output configuration: `DeviceId,DeviceStatus`

![Property columns](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

The following screenshot shows output message properties inspected in EventHub through [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Event custom properties](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## Partitioning

The following table summarizes the partition support and the number of output writers for each output type:

| Output type | Partitioning support | Partition key  | Number of output writers |
| --- | --- | --- | --- |
| Azure Data Lake Store | Yes | Use {date} and {time} tokens in the path prefix pattern. Choose the date format, such as YYYY/MM/DD, DD/MM/YYYY, or MM-DD-YYYY. HH is used for the time format. | Follows the input partitioning for [fully parallelizable queries](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Yes | Based on the PARTITION BY clause in the query. | Follows the input partitioning for [fully parallelizable queries](stream-analytics-scale-jobs.md). To learn more about achieving better write throughput performance when you're loading data into Azure SQL Database, see [Azure Stream Analytics output to Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob storage | Yes | Use {date} and {time} tokens from your event fields in the path pattern. Choose the date format, such as YYYY/MM/DD, DD/MM/YYYY, or MM-DD-YYYY. HH is used for the time format. Blob output can be partitioned by a single custom event attribute {fieldname} or {datetime:\<specifier>}. | Follows the input partitioning for [fully parallelizable queries](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Yes | Yes | Varies depending on partition alignment.<br /> When the partition key for event hub output is equally aligned with the upstream (previous) query step, the number of writers is the same as the number of partitions in event hub output. Each writer uses the [EventHubSender class](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) to send events to the specific partition. <br /> When the partition key for event hub output is not aligned with the upstream (previous) query step, the number of writers is the same as the number of partitions in that prior step. Each writer uses the [SendBatchAsync class](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) in **EventHubClient** to send events to all the output partitions. |
| Power BI | No | None | Not applicable. |
| Azure Table storage | Yes | Any output column.  | Follows the input partitioning for [fully parallelized queries](stream-analytics-scale-jobs.md). |
| Azure Service Bus topic | Yes | Automatically chosen. The number of partitions is based on the [Service Bus SKU and size](../service-bus-messaging/service-bus-partitioning.md). The partition key is a unique integer value for each partition.| Same as the number of partitions in the output topic.  |
| Azure Service Bus queue | Yes | Automatically chosen. The number of partitions is based on the [Service Bus SKU and size](../service-bus-messaging/service-bus-partitioning.md). The partition key is a unique integer value for each partition.| Same as the number of partitions in the output queue. |
| Azure Cosmos DB | Yes | Use the {partition} token in the collection name pattern. The {partition} value is based on the PARTITION BY clause in the query. | Follows the input partitioning for [fully parallelized queries](stream-analytics-scale-jobs.md). |
| Azure Functions | No | None | Not applicable. |

If your output adapter is not partitioned, lack of data in one input partition will cause a delay up to the late arrival amount of time. In such cases, the output is merged to a single writer, which might cause bottlenecks in your pipeline. To learn more about late arrival policy, see [Azure Stream Analytics event order considerations](stream-analytics-out-of-order-and-late-events.md).

## Output batch size
Azure Stream Analytics uses variable-size batches to process events and write to outputs. Typically the Stream Analytics engine doesn't write one message at a time, and uses batches for efficiency. When the rate of both the incoming and outgoing events is high, Stream Analytics uses larger batches. When the egress rate is low, it uses smaller batches to keep latency low.

The following table explains some of the considerations for output batching:

| Output type |	Max message size | Batch size optimization |
| :--- | :--- | :--- |
| Azure Data Lake Store | See [Data Lake Storage limits](../azure-subscription-service-limits.md#data-lake-store-limits). | Use up to 4 MB per write operation. |
| Azure SQL Database | 10,000 maximum rows per single bulk insert.<br />100 minimum rows per single bulk insert. <br />See [Azure SQL limits](../sql-database/sql-database-resource-limits.md). |  Every batch is initially bulk inserted with maximum batch size. You can split the batch in half (until you reach the minimum batch size) based on retryable errors from SQL. |
| Azure Blob storage | See [Azure Storage limits](../azure-subscription-service-limits.md#storage-limits). | The maximum blob block size is 4 MB.<br />The maximum blob bock count is 50,000. |
| Azure Event Hubs	| 256 KB per message. <br />See [Event Hubs limits](../event-hubs/event-hubs-quotas.md). |	When input/output partitioning isn't aligned, each event is packed individually in **EventData** and sent in a batch of up to the maximum message size (1 MB for the Premium SKU). <br /><br />  When input/output partitioning is aligned, multiple events are packed into a single **EventData** instance, up to the maximum message size, and sent.	|
| Power BI | See [Power BI Rest API limits](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table storage | See [Azure Storage limits](../azure-subscription-service-limits.md#storage-limits). | The default is 100 entities per single transaction. You can configure it to a smaller value as needed. |
| Azure Service Bus queue	| 256 KB per message.<br /> See [Service Bus limits](../service-bus-messaging/service-bus-quotas.md). | Use a single event per message. |
| Azure Service Bus topic | 256 KB per message.<br /> See [Service Bus limits](../service-bus-messaging/service-bus-quotas.md). | Use a single event per message. |
| Azure Cosmos DB	| See [Azure Cosmos DB limits](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batch size and write frequency are adjusted dynamically based Azure Cosmos DB responses. <br /> There are no predetermined limitations from Stream Analytics. |
| Azure Functions	| | The default batch size is 262,144 bytes (256 KB). <br /> The default event count per batch is 100. <br /> The batch size is configurable and can be increased or decreased in the Stream Analytics [output options](#azure-functions).

## Next steps
> [!div class="nextstepaction"]
> 
> [Quickstart: Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
