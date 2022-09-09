---
title: Import and Export data between serverless Apache Spark pools (preview) and SQL pools
description: This article provides information on how to use the custom connector for moving data between dedicated SQL pools and serverless Apache Spark pools (preview).
services: synapse-analytics 
author: euangMS 
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020 
ms.author: prgomata
ms.reviewer: euang
---
# Introduction

The Azure Synapse Apache Spark to Synapse SQL connector is designed to efficiently transfer data between serverless Apache Spark pools (preview) and SQL pools in Azure Synapse. The Azure Synapse Apache Spark to Synapse SQL connector works on dedicated SQL pools only, it doesn't work with serverless SQL pool.

## Design

Transferring data between Spark pools and SQL pools can be done using JDBC. However, given two distributed systems such as Spark and SQL pools, JDBC tends to be a bottleneck with serial data transfer.

The Azure Synapse Apache Spark pool to Synapse SQL connector is a data source implementation for Apache Spark. It uses the Azure Data Lake Storage Gen2 and Polybase in dedicated SQL pools to efficiently transfer data between the Spark cluster and the Synapse SQL instance.

![Connector Architecture](./media/synapse-spark-sqlpool-import-export/arch1.png)

## Authentication in Azure Synapse Analytics

Authentication between systems is made seamless in Azure Synapse Analytics. The Token Service connects with Azure Active Directory to obtain security tokens for use when accessing the storage account or the data warehouse server.

For this reason, there's no need to create credentials or specify them in the connector API as long as Azure AD-Auth is configured at the storage account and the data warehouse server. If not, SQL Auth can be specified. Find more details in the [Usage](#usage) section.

## Constraints

- This connector works only in Scala.

## Prerequisites

- Must be a member of **db_exporter** role in the database/SQL pool you want to transfer data to/from.
- Must be a member of Storage Blob Data Contributor role on the default storage account.

To create users, connect to the SQL pool database, and follow these examples:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

To assign a role:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## Usage

The import statements aren't required, they're pre-imported for the notebook experience.

### Transfer data to or from a dedicated SQL pool attached within the workspace

> [!NOTE]
> **Imports not needed in notebook experience**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### Read API

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

The above API will work for both Internal (Managed) as well as External Tables in the SQL pool.

#### Write API

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

The write API creates the table in the dedicated SQL pool and then invokes Polybase to load the data.  The table must not exist in the dedicated SQL pool or an error will be returned stating that "There is already an object named..."

TableType values

- Constants.INTERNAL - Managed table in dedicated SQL pool
- Constants.EXTERNAL - External table in dedicated SQL pool

SQL pool-managed table

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

SQL pool external table

To write to a dedicated SQL pool external table, an EXTERNAL DATA SOURCE and an EXTERNAL FILE FORMAT must exist on the dedicated SQL pool.  For more information, read [creating an external data source](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) and [external file formats](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in dedicated SQL pool.  Below are examples for creating an external data source and external file formats in dedicated SQL pool.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

An EXTERNAL CREDENTIAL object is not necessary when using Azure Active Directory pass-through authentication to the storage account.  Ensure you are a member of the "Storage Blob Data Contributor" role on the storage account.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### Transfer data to or from a dedicated SQL pool or database outside the workspace

> [!NOTE]
> Imports not needed in notebook experience

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### Read API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### Write API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### Use SQL Auth instead of Azure AD

#### Read API

Currently the connector doesn't support token-based auth to a dedicated SQL pool that is outside of the workspace. You'll need to use SQL Auth.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### Write API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### Use the PySpark connector

> [!NOTE]
> This example is given with only the notebook experience kept in mind.

Assume you have a dataframe "pyspark_df" that you want to write into the DW.

Create a temp table using the dataframe in PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Run a Scala cell in the PySpark notebook using magics:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Similarly, in the read scenario, read the data using Scala and write it into a temp table, and use Spark SQL in PySpark to query the temp table into a dataframe.

## Allow other users to use the Azure Synapse Apache Spark to Synapse SQL connector in your workspace

You need to be Storage Blob Data Owner on the ADLS Gen2 storage account connected to the workspace to alter missing permissions for others. Ensure the user has access to the workspace and permissions to run notebooks.

### Option 1

- Make the user a Storage Blob Data Contributor/Owner

### Option 2

- Specify the following ACLs on the folder structure:

| Folder | / | synapse | workspaces  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Access Permissions | --X | --X | --X | --X | --X | --X | -WX |
| Default Permissions | ---| ---| ---| ---| ---| ---| ---|

- You should be able to ACL all folders from "synapse" and downward from Azure portal. To ACL the root "/" folder, please follow the instructions below.

- Connect to the storage account connected with the workspace from Storage Explorer using Azure AD
- Select your Account and give the ADLS Gen2 URL and default file system for the workspace
- Once you can see the storage account listed, right-click on the listing workspace and select "Manage Access"
- Add the User to the / folder with "Execute" Access Permission. Select "Ok"

> [!IMPORTANT]
> Make sure you don't select "Default" if you don't intend to.

## Next steps

- [Create a dedicated SQL pool using the Azure portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Create a new Apache Spark pool using the Azure portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
