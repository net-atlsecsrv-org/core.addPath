---
title: Copy data from Teradata by using Azure Data Factory | Microsoft Docs
description: The Teradata Connector of the Data Factory service lets you copy data from a Teradata database to data stores supported by Data Factory as sinks. 
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na

ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang

---
# Copy data from Teradata by using Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Current version](connector-teradata.md)

This article outlines how to use the copy activity in Azure Data Factory to copy data from a Teradata database. It builds on the [copy activity overview](copy-activity-overview.md).

## Supported capabilities

You can copy data from a Teradata database to any supported sink data store. For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Specifically, this Teradata connector supports:

- Teradata **version 14.10, 15.0, 15.10, 16.0, 16.10, and 16.20**.
- Copying data by using **Basic** or **Windows** authentication.
- Parallel copying from a Teradata source. See the [Parallel copy from Teradata](#parallel-copy-from-teradata) section for details.

> [!NOTE]
>
> After the release of self-hosted integration runtime v3.18, Azure Data Factory upgraded the Teradata connector. Any existing workload that uses the previous Teradata connector is still supported. For new workloads, however, it's a good idea to use the new one. Note that the new path requires a different set of linked service, dataset, and copy source. For configuration details, see the respective sections that follow.

## Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

The integration runtime provides a built-in Teradata driver, starting from version 3.18. You don't need to manually install any driver. The driver requires "Visual C++ Redistributable 2012 Update 4" on the self-hosted integration runtime machine. If you don't yet have it installed, download it from [here](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

For any self-hosted integration runtime version earlier than 3.18, install the [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886), version 14 or later, on the integration runtime machine. 

## Getting started

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

The following sections provide details about properties that are used to define Data Factory entities specific to the Teradata connector.

## Linked service properties

The Teradata linked service supports the following properties:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to **Teradata**. | Yes |
| connectionString | Specifies the information needed to connect to the Teradata Database instance. Refer to the following samples.<br/>You can also put a password in Azure Key Vault, and pull the `password` configuration out of the connection string. Refer to [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md) with more details. | Yes |
| username | Specify a user name to connect to the Teradata database. Applies when you are using Windows authentication. | No |
| password | Specify a password for the user account you specified for the user name. You can also choose to [reference a secret stored in Azure Key Vault](store-credentials-in-key-vault.md). <br>Applies when you are using Windows authentication, or referencing a password in Key Vault for basic authentication. | No |
| connectVia | The [Integration Runtime](concepts-integration-runtime.md) to be used to connect to the data store. Learn more from [Prerequisites](#prerequisites) section. If not specified, it uses the default Azure Integration Runtime. |Yes |

**Example using basic authentication**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Example using Windows authentication**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> The following payload is still supported. Going forward, however, you should use the new one.

**Previous payload:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## Dataset properties

This section provides a list of properties supported by the Teradata dataset. For a full list of sections and properties available for defining datasets, see [Datasets](concepts-datasets-linked-services.md).

To copy data from Teradata, the following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to `TeradataTable`. | Yes |
| database | The name of the Teradata database. | No (if "query" in activity source is specified) |
| table | The name of the table in the Teradata database. | No (if "query" in activity source is specified) |

**Example:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` type dataset is still supported. However, we recommend that you use the new dataset.

**Previous payload:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## Copy activity properties

This section provides a list of properties supported by Teradata source. For a full list of sections and properties available for defining activities, see [Pipelines](concepts-pipelines-activities.md). 

### Teradata as source

>[!TIP]
>To load data from Teradata efficiently by using data partitioning, see the [Parallel copy from Teradata](#parallel-copy-from-teradata) section.

To copy data from Teradata, the following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to `TeradataSource`. | Yes |
| query | Use the custom SQL query to read data. An example is `"SELECT * FROM MyTable"`.<br>When you enable partitioned load, you need to hook any corresponding built-in partition parameters in your query. For examples, see the [Parallel copy from Teradata](#parallel-copy-from-teradata) section. | No (if table in dataset is specified) |
| partitionOptions | Specifies the data partitioning options used to load data from Teradata. <br>Allow values are: **None** (default), **Hash** and **DynamicRange**.<br>When a partition option is enabled (that is, not `None`), also configure the [`parallelCopies`](copy-activity-performance.md#parallel-copy) setting on the copy activity. This determines the parallel degree to concurrently load data from a Teradata database. For example, you might set this to 4. | No |
| partitionSettings | Specify the group of the settings for data partitioning. <br>Apply when partition option isn't `None`. | No |
| partitionColumnName | Specify the name of the source column **in integer type** that will be used by range partitioning for parallel copy. If not specified, the primary key of the table is auto-detected and used as the partition column. <br>Apply when the partition option is `Hash` or `DynamicRange`. If you use a query to retrieve the source data, hook `?AdfHashPartitionCondition` or  `?AdfRangePartitionColumnName` in WHERE clause. See example in [Parallel copy from Teradata](#parallel-copy-from-teradata) section. | No |
| partitionUpperBound | The maximum value of the partition column to copy data out. <br>Apply when partition option is `DynamicRange`. If you use query to retrieve source data, hook `?AdfRangePartitionUpbound` in the WHERE clause. For an example, see the [Parallel copy from Teradata](#parallel-copy-from-teradata) section. | No |
| partitionLowerBound | The minimum value of the partition column to copy data out. <br>Apply when the partition option is `DynamicRange`. If you use a query to retrieve the source data, hook `?AdfRangePartitionLowbound` in the WHERE clause. For an example, see the [Parallel copy from Teradata](#parallel-copy-from-teradata) section. | No |

> [!NOTE]
>
> `RelationalSource` type copy source is still supported, but it doesn't support the new built-in parallel load from Teradata (partition options). However, we recommend that you use the new dataset.

**Example: copy data by using a basic query without partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## Parallel copy from Teradata

The Data Factory Teradata connector provides built-in data partitioning to copy data from Teradata in parallel. You can find data partitioning options on the **Source** table of the copy activity.

![Screenshot of partition options](./media/connector-teradata/connector-teradata-partition-options.png)

When you enable partitioned copy, Data Factory runs parallel queries against your Teradata source to load data by partitions. The parallel degree is controlled by the [`parallelCopies`](copy-activity-performance.md#parallel-copy) setting on the copy activity. For example, if you set `parallelCopies` to four, Data Factory concurrently generates and runs four queries based on your specified partition option and settings. Each query retrieves a portion of data from your Teradata database.

It's a good idea to enable parallel copy with data partitioning, especially when you load large amount of data from your Teradata database. The following are suggested configurations for different scenarios:

| Scenario                                                     | Suggested settings                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Full load from large table.                                   | **Partition option**: Hash. <br><br/>During execution, Data Factory automatically detects the PK column, applies a hash against it, and copies data by partitions. |
| Load large amount of data by using a custom query.                 | **Partition option**: Hash.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Partition column**: Specify the column used for apply hash partition. If not specified, Data Factory automatically detects the PK column of the table you specified in the Teradata dataset.<br><br>During execution, Data Factory replaces `?AdfHashPartitionCondition` with the hash partition logic, and sends to Teradata. |
| Load large amount of data by using a custom query, having an integer column with evenly distributed value for range partitioning. | **Partition options**: Dynamic range partition.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Partition column**: Specify the column used to partition data. You can partition against the column with integer data type.<br>**Partition upper bound** and **partition lower bound**: Specify if you want to filter against the partition column to retrieve data only between the lower and upper range.<br><br>During execution, Data Factory replaces `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, and `?AdfRangePartitionLowbound` with the actual column name and value ranges for each partition, and sends to Teradata. <br>For example, if your partition column "ID" set with the lower bound as 1 and the upper bound as 80, with parallel copy set as 4, Data Factory retrieves data by 4 partitions. Their IDs are between [1,20], [21, 40], [41, 60], and [61, 80], respectively. |

**Example: query with hash partition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Example: query with dynamic range partition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## Data type mapping for Teradata

When you copy data from Teradata, the following mappings apply. To learn about how the copy activity maps the source schema and data type to the sink, see [Schema and data type mappings](copy-activity-schema-and-type-mapping.md).

| Teradata data type | Data Factory interim data type |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Not supported. Apply explicit cast in source query. |
| Integer |Int32 |
| Interval Day |Not supported. Apply explicit cast in source query. |
| Interval Day To Hour |Not supported. Apply explicit cast in source query. |
| Interval Day To Minute |Not supported. Apply explicit cast in source query. |
| Interval Day To Second |Not supported. Apply explicit cast in source query. |
| Interval Hour |Not supported. Apply explicit cast in source query. |
| Interval Hour To Minute |Not supported. Apply explicit cast in source query. |
| Interval Hour To Second |Not supported. Apply explicit cast in source query. |
| Interval Minute |Not supported. Apply explicit cast in source query. |
| Interval Minute To Second |Not supported. Apply explicit cast in source query. |
| Interval Month |Not supported. Apply explicit cast in source query. |
| Interval Second |Not supported. Apply explicit cast in source query. |
| Interval Year |Not supported. Apply explicit cast in source query. |
| Interval Year To Month |Not supported. Apply explicit cast in source query. |
| Number |Double |
| Period (Date) |Not supported. Apply explicit cast in source query. |
| Period (Time) |Not supported. Apply explicit cast in source query. |
| Period (Time With Time Zone) |Not supported. Apply explicit cast in source query. |
| Period (Timestamp) |Not supported. Apply explicit cast in source query. |
| Period (Timestamp With Time Zone) |Not supported. Apply explicit cast in source query. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Not supported. Apply explicit cast in source query. |
| Xml |Not supported. Apply explicit cast in source query. |


## Next steps
For a list of data stores supported as sources and sinks by the copy activity in Data Factory, see [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats).
