---
title: Mapping Data Flow performance and tuning guide in Azure Data Factory | Microsoft Docs
description: Learn about key factors that affect the performance of data flows in Azure Data Factory when you use Mapping Data Flows.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
---

# Mapping data flows performance and tuning guide

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flows provide a code-free browser interface to design, deploy, and orchestrate data transformations at scale.

> [!NOTE]
> If you are not familiar with ADF Mapping Data Flows in general, see [Data Flows Overview](concepts-data-flow-overview.md) before reading this article.
>

> [!NOTE]
> When you are designing and testing Data Flows from the ADF UI, make sure to turn on the Debug switch so that you can execute your data flows in real-time without waiting for a cluster to warm up.
>

![Debug Button](media/data-flow/debugb1.png "Debug")

## Optimizing for Azure SQL Database and Azure SQL Data Warehouse

![Source Part](media/data-flow/sourcepart2.png "Source Part")

### You can match Spark data partitioning to your source database partitioning based on a database table column key in the source transformation

* Go to "Optimize" and select "Source". Set either a specific table column or a type in a query.
* If you chose "column", then pick the partition column.
* Also, set the maximum number of connections to your Azure SQL DB. You can try a higher setting to gain parallel connections to your database. However, some cases may result in faster performance with a limited number of connections.

### Set batch size and query on source

![Source](media/data-flow/source4.png "Source")

* Setting batch size will instruct ADF to store data in sets in memory instead of row-by-row. It is an optional setting and you may run out of resources on the compute nodes if they are not sized properly.
* Setting a query can allow you to filter rows right at the source before they even arrive for Data Flow for processing, which can make the initial data acquisition faster.
* If you use a query, you can add optional query hints for your Azure SQL DB, i.e. READ UNCOMMITTED

### Set sink batch size

![Sink](media/data-flow/sink4.png "Sink")

* In order to avoid row-by-row processing of your data floes, set the "Batch size" in the sink settings for Azure SQL DB. This will tell ADF to process database writes in batches based on the size provided.

### Set partitioning options on your sink

* Even if you don't have your data partitioned in your destination Azure SQL DB tables, go to the Optimize tab and set partitioning.
* Very often, simply telling ADF to use Round Robin partitioning on the Spark execution clusters results in much faster data loading instead of forcing all connections from a single node/partition.

### Increase size of your compute engine in Azure Integration Runtime

![New IR](media/data-flow/ir-new.png "New IR")

* Increase the number of cores, which will increase the number of nodes, and provide you with more processing power to query and write to your Azure SQL DB.
* Try "Compute Optimized" and "Memory Optimized" options to apply more resources to your compute nodes.

### Unit test and performance test with debug

* When unit testing data flows, set the "Data Flow Debug" button to ON.
* Inside of the Data Flow designer, use the Data Preview tab on transformations to view the results of your transformation logic.
* Unit test your data flows from the pipeline designer by placing a Data Flow activity on the pipeline design canvas and use the "Debug" button to test.
* Testing in debug mode will work against a live warmed cluster environment without the need to wait for a just-in-time cluster spin-up.

### Disable indexes on write
* Use an ADF pipeline stored procedure activity prior to your Data Flow activity that disables indexes on your target tables that are being written to from your Sink.
* After your Data Flow activity, add another stored proc activity that enabled those indexes.

### Increase the size of your Azure SQL DB
* Schedule a resizing of your source and sink Azure SQL DB before your run your pipeline to increase the throughput and minimize Azure throttling once you reach DTU limits.
* After your pipeline execution is complete, you can resize your databases back to their normal run rate.

## Optimizing for Azure SQL Data Warehouse

### Use staging to load data in bulk via Polybase

* In order to avoid row-by-row processing of your data floes, set the "Staging" option in the Sink settings so that ADF can leverage Polybase to avoid row-by-row inserts into DW. This will instruct ADF to use Polybase so that data can be loaded in bulk.
* When you execute your data flow activity from a pipeline, with Staging turned on, you will need to select the Blob store location of your staging data for bulk loading.

### Increase the size of your Azure SQL DW

* Schedule a resizing of your source and sink Azure SQL DW before you run your pipeline to increase the throughput and minimize Azure throttling once you reach DWU limits.

* After your pipeline execution is complete, you can resize your databases back to their normal run rate.

## Optimize for files

* You can control how many partitions that ADF will use. On each Source & Sink transformation, as well as each individual transformation, you can set a partitioning scheme. For smaller files, you may find selecting "Single Partition" can sometimes work better and faster than asking Spark to partition your small files.
* If you do not have enough information about your source data, you can choose "Round Robin" partitioning and set the number of partitions.
* If you explore your data and find that you have columns that can be good hash keys, use the Hash partitioning option.

### File naming options

* The default nature of writing transformed data in ADF Mapping Data Flows is to write to a dataset that has a Blob or ADLS Linked Service. You should set that dataset to point to a folder or container, not a named file.
* Data Flows use Azure Databricks Spark for execution, which means that your output will be split over multiple files based on either default Spark partitioning or the partitioning scheme that you've explicitly chosen.
* A very common operation in ADF Data Flows is to choose "Output to single file" so that all of your output PART files are merged together into a single output file.
* However, this operation requires that the output reduces to a single partition on a single cluster node.
* Keep this in mind when choosing this popular option. You can run out of cluster node resources if you are combining many large source files into a single output file partition.
* To avoid exhausting compute node resources, you can keep the default or explicit partitioning scheme in ADF, which optimizes for performance, and then add a subsequent Copy Activity in the pipeline that merges all of the PART files from the output folder to a new single file. Essentially, this technique separates the action of transformation from file merging and achieves the same result as setting "output to single file".

## Next steps
See the other Data Flow articles:

- [Data Flow overview](concepts-data-flow-overview.md)
- [Data Flow activity](control-flow-execute-data-flow-activity.md)

