---
title: Query Azure Cosmos DB data using a serverless SQL pool in Azure Synapse Link Preview
description: In this article, you'll learn how to query Azure Cosmos DB by using a serverless SQL pool in Azure Synapse Link Preview.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 12/04/2020
ms.author: jovanpop
ms.reviewer: jrasnick
---

# Query Azure Cosmos DB data with a serverless SQL pool in Azure Synapse Link Preview

> [!IMPORTANT]
> Serverless SQL pool support for Azure Synapse Link for Azure Cosmos DB is currently in preview. This preview version is provided without a service level agreement, and it's not recommended for production workloads. For more information, see [Supplemental terms of use for Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A serverless SQL pool allows you to analyze data in your Azure Cosmos DB containers that are enabled with [Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) in near real time without affecting the performance of your transactional workloads. It offers a familiar T-SQL syntax to query data from the [analytical store](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) and integrated connectivity to a wide range of business intelligence (BI) and ad-hoc querying tools via the T-SQL interface.

For querying Azure Cosmos DB, the full [SELECT](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) surface area is supported through the [OPENROWSET](develop-openrowset.md) function, which includes the majority of [SQL functions and operators](overview-features.md). You can also store results of the query that reads data from Azure Cosmos DB along with data in Azure Blob Storage or Azure Data Lake Storage by using [create external table as select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). You can't currently store serverless SQL pool query results to Azure Cosmos DB by using CETAS.

In this article, you'll learn how to write a query with a serverless SQL pool that will query data from Azure Cosmos DB containers that are enabled with Azure Synapse Link. You can then learn more about building serverless SQL pool views over Azure Cosmos DB containers and connecting them to Power BI models in [this tutorial](./tutorial-data-analyst.md).

> [!IMPORTANT]
> This tutorial uses a container with an [Azure Cosmos DB well-defined schema](../../cosmos-db/analytical-store-introduction.md#schema-representation). The query experience that the serverless SQL pool provides for an [Azure Cosmos DB full fidelity schema](#full-fidelity-schema) is temporary behavior that will change based on the preview feedback. Don't rely on the result set schema of the `OPENROWSET` function without the `WITH` clause that reads data from a container with a full fidelity schema because the query experience might be aligned with and change based on the well-defined schema. You can post your feedback in the [Azure Synapse Analytics feedback forum](https://feedback.azure.com/forums/307516-azure-synapse-analytics). You can also contact the [Azure Synapse Link product team](mailto:cosmosdbsynapselink@microsoft.com) to provide feedback.

## Overview

Serverless SQL pool enables you to query Azure Cosmos DB analytical storage using `OPENROWSET` function. 
- `OPENROWSET` with inline key. This syntax can be used to query Azure Cosmos DB collections without need to prepare credentials.
- `OPENROWSET` that referenced credential that contains the Cosmos DB account key. This syntax can be used to create views on Azure Cosmos DB collections.

### [OPENROWSET with key](#tab/openrowset-key)

To support querying and analyzing data in an Azure Cosmos DB analytical store, a serverless SQL pool uses the following `OPENROWSET` syntax:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

The Azure Cosmos DB connection string specifies the Azure Cosmos DB account name, database name, database account master key, and an optional region name to the `OPENROWSET` function.

The connection string has the following format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

The Azure Cosmos DB container name is specified without quotation marks in the `OPENROWSET` syntax. If the container name has any special characters, for example, a dash (-), the name should be wrapped within square brackets (`[]`) in the `OPENROWSET` syntax.

### [OPENROWSET with credential](#tab/openrowset-credential)

You can use `OPENROWSET` syntax that references credential:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

The Azure Cosmos DB connection string doesn't contain key in this case. The connection string has the following format:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Database account master key is placed in server-level credential or database scoped credential. 

---

> [!IMPORTANT]
> Make sure that you're using some UTF-8 database collation, for example, `Latin1_General_100_CI_AS_SC_UTF8`, because string values in an Azure Cosmos DB analytical store are encoded as UTF-8 text.
> A mismatch between text encoding in the file and collation might cause unexpected text conversion errors.
> You can easily change default collation of the current database by using the T-SQL statement 
`alter database current collate Latin1_General_100_CI_AI_SC_UTF8`.

> [!NOTE]
> A serverless SQL pool doesn't support querying an Azure Cosmos DB transactional store.

## Sample dataset

The examples in this article are based on data from the [European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) and [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

You can see the license and the structure of data on these pages. You can also download sample data for the [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) and [CORD-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) datasets.

To follow along with this article showcasing how to query Azure Cosmos DB data with a serverless SQL pool, make sure that you create the following resources:

* An Azure Cosmos DB database account that's [Azure Synapse Link enabled](../../cosmos-db/configure-synapse-link.md).
* An Azure Cosmos DB database named `covid`.
* Two Azure Cosmos DB containers named `Ecdc` and `Cord19` loaded with the preceding sample datasets.

You can use the following connection string for testing purpose: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==`. Note that this connection will not guarantee performance because this account might be located in remote region compared to your Synapse SQL endpoint.

## Explore Azure Cosmos DB data with automatic schema inference

The easiest way to explore data in Azure Cosmos DB is by using the automatic schema inference capability. By omitting the `WITH` clause from the `OPENROWSET` statement, you can instruct the serverless SQL pool to autodetect (infer) the schema of the analytical store of the Azure Cosmos DB container.

### [OPENROWSET with key](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### [OPENROWSET with credential](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

In the preceding example, we instructed the serverless SQL pool to connect to the `covid` database in the Azure Cosmos DB account `MyCosmosDbAccount` authenticated by using the Azure Cosmos DB key (the dummy in the preceding example). We then accessed the `Ecdc` container's analytical store in the `West US 2` region. Since there's no projection of specific properties, the `OPENROWSET` function will return all properties from the Azure Cosmos DB items.

Assuming that the items in the Azure Cosmos DB container have `date_rep`, `cases`, and `geo_id` properties, the results of this query are shown in the following table:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

If you need to explore data from the other container in the same Azure Cosmos DB database, you can use the same connection string and reference the required container as the third parameter:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## Explicitly specify schema

While automatic schema inference capability in `OPENROWSET` provides a simple, easy-to-use querience, your business scenarios might require you to explicitly specify the schema to read-only relevant properties from the Azure Cosmos DB data.

The `OPENROWSET` function enables you to explicitly specify what properties you want to read from the data in the container and to specify their data types.

Let's imagine that we've imported some data from the [ECDC COVID dataset](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) with the following structure into Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

These flat JSON documents in Azure Cosmos DB can be represented as a set of rows and columns in Synapse SQL. The `OPENROWSET` function enables you to specify a subset of properties that you want to read and the exact column types in the `WITH` clause:

### [OPENROWSET with key](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### [OPENROWSET with credential](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
The result of this query might look like the following table:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

For more information about the SQL types that should be used for Azure Cosmos DB values, see the [rules for SQL type mappings](#azure-cosmos-db-to-sql-type-mappings) at the end of the article.

## Create view

Once you identify the schema, you can prepare a view on top of your Azure Cosmos DB data. You should place your Azure Cosmos DB account key in a separate credential and reference this credential from `OPENROWSET` function. Do not keep your account key in the view definition.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Do not use `OPENROWSET` without explicitly defined schema because it might impact your performance. Make sure that you use the smallest possible sizes for your columns (for example VARCHAR(100) instead of default VARCHAR(8000)). You should use some UTF-8 collation as default database collation or set it as explicit column collation to avoid [UTF-8 conversion issue](/troubleshoot/reading-utf8-text). Collation `Latin1_General_100_BIN2_UTF8` provides best performance when yu filter data using some string columns.

## Query nested objects and arrays

With Azure Cosmos DB, you can represent more complex data models by composing them as nested objects or arrays. The autosync capability of Azure Synapse Link for Azure Cosmos DB manages the schema representation in the analytical store out of the box, which includes handling nested data types that allow for rich querying from the serverless SQL pool.

For example, the [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) dataset has JSON documents that follow this structure:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

The nested objects and arrays in Azure Cosmos DB are represented as JSON strings in the query result when the `OPENROWSET` function reads them. You can specify the paths to nested values in the objects when you use the `WITH` clause:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id	varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

The result of this query might look like the following table:

| paper_id | title | metadata | authors |
| --- | --- | --- |
| bb11206963e831f… | Supplementary Information An eco-epidemi… | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1… | The Use of Convalescent Sera in Immune-E… | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649… | Tylosema esculentum (Marama) Tuber and B… | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Learn more about analyzing [complex data types in Azure Synapse Link](../how-to-analyze-complex-schema.md) and [nested structures in a serverless SQL pool](query-parquet-nested-types.md).

> [!IMPORTANT]
> If you see unexpected characters in your text like `MÃƒÂ©lade` instead of `Mélade`, then your database collation isn't set to [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) collation.
> [Change collation of the database](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) to UTF-8 collation by using a SQL statement like `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`.

## Flatten nested arrays

Azure Cosmos DB data might have nested subarrays like the author's array from a [CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) dataset:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

In some cases, you might need to "join" the properties from the top item (metadata) with all
elements of the array (authors). A serverless SQL pool enables you to flatten nested structures by applying
the `OPENJSON` function on the nested array:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

The result of this query might look like the following table:

| title | authors | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |	`[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `	{"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |	`[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana |	`{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |	`[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |		

> [!IMPORTANT]
> If you see unexpected characters in your text like `MÃƒÂ©lade` instead of `Mélade`, then your database collation isn't set to [UTF-8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) collation. [Change collation of the database](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) to UTF-8 collation by using a SQL statement like `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8`.

## Azure Cosmos DB to SQL type mappings

Although Azure Cosmos DB transactional store is schema-agnostic, the analytical store is schematized to optimize for analytical query performance. With the autosync capability of Azure Synapse Link, Azure Cosmos DB manages the schema representation in the analytical store out of the box, which includes handling nested data types. Since a serverless SQL pool queries the analytical store, it's important to understand how to map Azure Cosmos DB input data types to SQL data types.

Azure Cosmos DB accounts of SQL (Core) API support JSON property types of number, string, Boolean, null, nested object, or array. You would need to choose SQL types that match these JSON types if you're using the `WITH` clause in `OPENROWSET`. The following table shows the SQL column types that should be used for different property types in Azure Cosmos DB.

| Azure Cosmos DB property type | SQL column type |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (UTF-8 database collation) |
| Date time (ISO-formatted string) | varchar(30) |
| Date time (UNIX timestamp) | bigint |
| Null | `any SQL type` 
| Nested object or array | varchar(max) (UTF-8 database collation), serialized as JSON text |

## Full fidelity schema

Azure Cosmos DB full fidelity schema records both values and their best match types for every property in a container. The `OPENROWSET` function on a container with full fidelity schema provides both the type and the actual value in each cell. Let's assume that the following query reads the items from a container with full fidelity schema:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

The result of this query will return types and values formatted as JSON text:

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

For every value, you can see the type identified in an Azure Cosmos DB container item. Most of the values for the `date_rep` property contain `date` values, but some of them are incorrectly stored as strings in Azure Cosmos DB. Full fidelity schema will return both correctly typed `date` values and incorrectly formatted `string` values.
The number of cases is information stored as an `int32` value, but there's one value that's entered as a decimal number. This value has the `float64` type. If there are some values that exceed the largest `int32` number, they would be stored as the `int64` type. All `geo_id` values in this example are stored as `string` types.

> [!IMPORTANT]
> The `OPENROWSET` function without a `WITH` clause exposes both values with expected types and the values with incorrectly entered types. This function is designed for data exploration and not for reporting. Don't parse JSON values returned from this function to build reports. Use an explicit [WITH clause](#query-items-with-full-fidelity-schema) to create your reports. You should clean up the values that have incorrect types in the Azure Cosmos DB container to apply corrections in the full fidelity analytical store.

If you need to query Azure Cosmos DB accounts of the Mongo DB API kind, you can learn more about the full fidelity schema representation in the analytical store and the extended property names to be used in [What is Azure Cosmos DB Analytical Store (preview)?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### Query items with full fidelity schema

While querying full fidelity schema, you need to explicitly specify the SQL type and the expected Azure Cosmos DB property type in the `WITH` clause. Don't use `OPENROWSET` without a `WITH` clause in the reports because the format of the result set might be changed in the preview based on feedback.

In the following example, we'll assume that `string` is the correct type for the `geo_id` property and `int32` is the correct type for the `cases` property:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Values for `geo_id` and `cases` that have other types will be returned as `NULL` values. This query will reference only the `cases` with the specified type in the expression (`cases.int32`).

If you have values with other types (`cases.int64`, `cases.float64`) that can't be cleaned in an Azure Cosmos DB container, you would need to explicitly reference them in a `WITH` clause and combine the results. The following query aggregates both `int32`, `int64`, and `float64` stored in the `cases` column:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

In this example, the number of cases is stored either as `int32`, `int64`, or `float64` values. All values must be extracted to calculate the number of cases per country.

## Known issues

- The query experience that serverless SQL pool provides for [Azure Cosmos DB full fidelity schema](#full-fidelity-schema) is temporary behavior that will be changed based on preview feedback. Don't rely on the schema that the `OPENROWSET` function without the `WITH` clause provides during public preview because the query experience might be aligned with well-defined schema based on customer feedback. To provide feedback, contact the [Azure Synapse Link product team](mailto:cosmosdbsynapselink@microsoft.com).
- A serverless SQL pool will return a compile-time warning if the `OPENROWSET` column collation doesn't have UTF-8 encoding. You can easily change the default collation for all `OPENROWSET` functions running in the current database by using the T-SQL statement `alter database current collate Latin1_General_100_CI_AS_SC_UTF8`.

Possible errors and troubleshooting actions are listed in the following table.

| Error | Root cause |
| --- | --- |
| Syntax errors:<br/> - Incorrect syntax near `Openrowset`<br/> - `...` is not a recognized `BULK OPENROWSET` provider option.<br/> - Incorrect syntax near `...` | Possible root causes:<br/> - Not using CosmosDB as the first parameter.<br/> - Using a string literal instead of an identifier in the third parameter.<br/> - Not specifying the third parameter (container name). |
| There was an error in the CosmosDB connection string. | - The account, database, or key isn't specified. <br/> - There's some option in a connection string that isn't recognized.<br/> - A semicolon (`;`) is placed at the end of a connection string. |
| Resolving CosmosDB path has failed with the error "Incorrect account name" or "Incorrect database name." | The specified account name, database name, or container can't be found, or analytical storage hasn't been enabled to the specified collection.|
| Resolving CosmosDB path has failed with the error "Incorrect secret value" or "Secret is null or empty." | The account key isn't valid or is missing. |
| Column `column name` of the type `type name` isn't compatible with the external data type `type name`. | The specified column type in the `WITH` clause doesn't match the type in the Azure Cosmos DB container. Try to change the column type as it's described in the section [Azure Cosmos DB to SQL type mappings](#azure-cosmos-db-to-sql-type-mappings), or use the `VARCHAR` type. |
| Column contains `NULL` values in all cells. | Possibly a wrong column name or path expression in the `WITH` clause. The column name (or path expression after the column type) in the `WITH` clause must match some property name in the Azure Cosmos DB collection. Comparison is *case-sensitive*. For example, `productCode` and `ProductCode` are different properties. |

You can report suggestions and issues on the [Azure Synapse Analytics feedback page](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## Next steps

For more information, see the following articles:

- [Use Power BI and serverless SQL pool with Azure Synapse Link](../../cosmos-db/synapse-link-power-bi.md)
- [Create and use views in a serverless SQL pool](create-use-views.md)
- [Tutorial on building serverless SQL pool views over Azure Cosmos DB and connecting them to Power BI models via DirectQuery](./tutorial-data-analyst.md)
