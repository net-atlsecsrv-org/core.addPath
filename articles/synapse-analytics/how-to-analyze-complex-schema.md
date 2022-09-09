---
title: Analyze schema with arrays and nested structures
description: How to analyze arrays and nested structures with Apache Spark and SQL
services: synapse-analytics 
author: ArnoMicrosoft
ms.service: synapse-analytics 
ms.topic: how-to
ms.subservice: 
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
---

# Analyze complex data types in Azure Synapse Analytics

This article is relevant for Parquet files and containers in [Synapse Link for Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). It explains how users can use Spark or SQL to read or transform data with complex schemas such as arrays or nested structures. The following example is completed with a single document but can easily scale to billions of documents with Spark or SQL. The code included in this article uses PySpark (Python).

## Use Case

Complex data types are increasingly common and represent a challenge for data engineers as analyzing nested schema and arrays tend to include time-consuming and complex SQL queries. Additionally, it can be difficult to rename or cast the nested columns data type. Also, performance issues arise when working with deeply nested objects.

Data Engineers need to understand how to efficiently process complex data types and make them easily accessible to everyone.

In the following example, Synapse Spark is used to read and transform objects into a flat structure through data frames. Synapse SQL serverless is used to query such objects directly and return those results as a regular table.

## What are arrays and nested structures?

The following object comes from [App Insight](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). In this object, there are nested structures and arrays that contain nested structures.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### Schema example of arrays and nested structures
When printing the schema of the object's data frame (called **df**) with the command `df.printschema`, we see the following representation:

* Yellow color represents nested structure
* Green color represents an array with two elements

[![Schema origin](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**, and **_etag** have been added to the system as the document was ingested into Azure Cosmos DB transactional store.

The data frame above counts for 5 columns and 1 row only. After transformation, the curated data frame will have 13 columns and 2 rows in a tabular format.

## Flatten nested structures and explode arrays with Apache Spark

With Synapse Spark, it's easy to transform nested structures into columns and array elements into multiple rows. The following steps can be used for implementation.

[![Spark transformations steps](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Step 1**: We define a function to flatten the nested schema. This function can be used without change. Create a cell in a [PySpark notebook](quickstart-apache-spark-notebook.md) with the following function:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Step 2**: Use the function to flatten the nested schema of the data frame (**df**) into a new data frame `df_flat`:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

The display function should return 10 columns and 1 row. The array and its nested elements are still there.

**Step 3**: Transform the array `context_custom_dimensions` in the data frame `df_flat` into a new dataframe `df_flat_explode`. In the following code, we also define which column to select:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

The display function should return 10 columns and 2 rows. The next step is to flatten nested schemas with the function defined in step 1.

**Step 4**: Use the function to flatten the nested schema of the data frame `df_flat_explode` into a new data frame `df_flat_explode_flat`:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

The display function should show 13 columns and 2 rows.

The function `printSchema` of the data frame `df_flat_explode_flat` returns the following result:

[![Schema final](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## Read arrays and nested structures directly with SQL serverless

Querying and creating views and tables over such objects is possible with SQL serverless.

First, depending how the data has been stored, users should use the following taxonomy. Everything shown in UPPER CASE is specific to your use case:

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet' (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account= ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY, SECRET='YOURSECRET' |'CosmosDB' (Synapse Link)|


> [!NOTE]
> SQL serverless will support Linked Service for Synapse Link for Azure Cosmos and AAD passthrough. The capability is currently under gated preview for Synapse Link.

Replace each field as follows:
* 'YOUR BULK ABOVE' = the connection string of the data source you connect to
* 'YOUR TYPE ABOVE' = the format you use to connect to the source

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
        	FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

There are two different types of operations:

The first operation type is indicated in the following line of code, which defines the column called `contextdataeventTime` that refers to the nested element: Context.Data.eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

This line will define the column called contextdataeventTime that refers to the nest element: Context>Data>eventTime

The second operation type uses `cross apply` to create new rows for each element under the array and then with defines each nested object similar to the first bullet point: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

If the array had 5 elements with 4 nested structures, SQL serverless would return 5 rows and 4 columns. SQL serverless can query in-place, map the array in 2 rows and, display all nested structures into columns.

## Next steps

* [Learn how to query Synapse Link for Azure Cosmos DB with Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Query parquet nested types](./sql/query-parquet-nested-types.md) 