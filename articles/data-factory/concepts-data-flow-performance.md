---
title: Mapping Data Flow performance and tuning guide in Azure Data Factory | Microsoft Docs
description: Learn about key factors that affect the performance of Mapping Data Flows in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
---

# Mapping Data Flows performance and tuning guide

Mapping Data Flows in Azure Data Factory provide a code-free interface to design, deploy, and orchestrate data transformations at scale. If you're not familiar with Mapping Data Flows, see the [Mapping Data Flow Overview](concepts-data-flow-overview.md).

When designing and testing Data Flows from the ADF UX, make sure to switch on debug mode to execute your data flows in real time without waiting for a cluster to warm up. For more information, see [Debug Mode](concepts-data-flow-debug-mode.md).

## Monitoring data flow performance

While designing mapping data flows, you can unit test each transformation by clicking on the data preview tab in the configuration panel. Once you verify your logic, test your data flow end-to-end as an activity in a pipeline. Add an Execute Data Flow activity and use the Debug button to test the performance of your data flow. To open the execution plan and performance profile of your data flow, click on the eyeglasses icon under 'actions' in the output tab of your pipeline.

![Data Flow Monitor](media/data-flow/mon002.png "Data Flow Monitor 2")

 You can use this information to estimate the performance of your data flow against different-sized data sources. For more information, see [Monitoring Mapping Data Flows](concepts-data-flow-monitoring.md).

![Data Flow Monitoring](media/data-flow/mon003.png "Data Flow Monitor 3")

 For pipeline debug runs, about one minute of cluster set-up time in your overall performance calculations is required for a warm cluster. If you're initializing the default Azure Integration Runtime, the spin up time may take about 5 minutes.

## Increasing compute size in Azure Integration Runtime

An Integration Runtime with more cores increases the number of nodes in the Spark compute environments and provides more processing power to read, write, and transform your data.
* Try a **Compute Optimized** cluster if you want your processing rate to be higher than your input rate
* Try a **Memory Optimized** cluster if you want to cache more data in memory.

![New IR](media/data-flow/ir-new.png "New IR")

For more information how to create an Integration Runtime, see [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).

### Increase the size of your debug cluster

By default, turning on debug will use the default Azure Integration runtime that is created automatically for each data factory. This default Azure IR is set for eight cores, four for a driver node and four for a worker node, using General Compute properties. As you test with larger data, you can increase the size of your debug cluster by creating an Azure IR with larger configurations and choose this new Azure IR when you switch on debug. This will instruct ADF to use this Azure IR for data preview and pipeline debug with data flows.

## Optimizing for Azure SQL Database and Azure SQL Data Warehouse

### Partitioning on source

1. Go to the **Optimize** tab and select **Set Partitioning**
1. Select **Source**.
1. Under **Number of partitions**, set the maximum number of connections to your Azure SQL DB. You can try a higher setting to gain parallel connections to your database. However, some cases may result in faster performance with a limited number of connections.
1. Select whether to partition by a specific table column or a query.
1. If you selected **Column**, pick the partition column.
1. If you selected **Query**, enter a query that matches the partitioning scheme of your database table. This query allows the source database engine to leverage partition elimination. Your source database tables don't need to be partitioned. If your source isn't already partitioned, ADF will still use data partitioning in the Spark transformation environment based on the key that you select in the Source transformation.

![Source Part](media/data-flow/sourcepart3.png "Source Part")

### Source batch size, input, and isolation level

Under **Source Options** in the source transformation, the following settings can affect performance:

* Batch size instructs ADF to store data in sets in memory instead of row-by-row. Batch size is an optional setting and you may run out of resources on the compute nodes if they aren't sized properly.
* Setting a query can allow you to filter rows at the source before they arrive in Data Flow for processing. This can make the initial data acquisition faster. If you use a query, you can add optional query hints for your Azure SQL DB such as READ UNCOMMITTED.
* Read uncommitted will provide faster query results on Source transformation

![Source](media/data-flow/source4.png "Source")

### Sink batch size

To avoid row-by-row processing of your data flows, set **Batch size** in the Settings tab for Azure SQL DB and Azure SQL DW sinks. If batch size is set, ADF processes database writes in batches based on the size provided.

![Sink](media/data-flow/sink4.png "Sink")

### Partitioning on sink

Even if you don't have your data partitioned in your destination tables, its recommended to have your data partitioned in the sink transformation. Partitioned data often results in much faster loading over forcing all connections to use a single node/partition. Go to the Optimize tab of your sink and select *Round Robin* partitioning to select the ideal number of partitions to write to your sink.

### Disable indexes on write

In your pipeline, add a [Stored Procedure activity](transform-data-using-stored-procedure.md) before your Data Flow activity that disables indexes on your target tables written from your Sink. After your Data Flow activity, add another Stored Procedure activity that enables those indexes.

### Increase the size of your Azure SQL DB and DW

Schedule a resizing of your source and sink Azure SQL DB and DW before your pipeline run to increase the throughput and minimize Azure throttling once you reach DTU limits. After your pipeline execution is complete, resize your databases back to their normal run rate.

### [Azure SQL DW only] Use staging to load data in bulk via Polybase

To avoid row-by-row inserts into your DW, check **Enable staging** in your Sink settings so that ADF can use [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase allows ADF to load the data in bulk.
* When you execute your data flow activity from a pipeline, you'll need to select a Blob or ADLS Gen2 storage location to stage your data during bulk loading.

## Optimizing for files

At each transformation, you can set the partitioning scheme you wish data factory to use in the Optimize tab.
* For smaller files, you may find selecting *Single Partition* can sometimes work better and faster than asking Spark to partition your small files.
* If you don't have enough information about your source data, choose *Round Robin* partitioning and set the number of partitions.
* If your data has columns that can be good hash keys, choose *Hash partitioning*.

When debugging in data preview and pipeline debug, the limit and sampling sizes for file-based source datasets only apply to the number of rows returned, not the number of rows read. This can affect the performance of your debug executions and possibly cause the flow to fail.
* Debug clusters are small single-node clusters by default and we recommend using sample small files for debugging. Go to Debug Settings and point to a small subset of your data using a temporary file.

    ![Debug Settings](media/data-flow/debugsettings3.png "Debug Settings")

### File naming options

The most common way to write transformed data in Mapping Data Flows writing Blob or ADLS file store. In your sink, you must select a dataset that points to a container or folder, not a named file. As Mapping Data Flow uses Spark for execution, your output is split over multiple files based on your partitioning scheme.

A common partitioning scheme is to choose _Output to single file_, which merges all output PART files into a single file in your sink. This operation requires the output reduces to a single partition on a single cluster node. You can run out of cluster node resources if you're combining many large source files into a single output file.

To avoid exhausting compute node resources, keep the default, optimized scheme in data flow, and add a Copy Activity in your pipeline that merges all of the PART files from the output folder to a new single file. This technique separates the action of transformation from file merging and achieves the same result as setting _Output to single file_.

### Looping through file lists

A Mapping Data Flow will execute better when the Source transformation iterates over multiple files instead of looping via the For Each activity. We recommend using wildcards or file lists in your source transformation. The Data Flow process will execute faster by allowing the looping to occur inside the Spark cluster. For more information, see [Wildcarding in Source Transformation](data-flow-source.md#file-based-source-options).

For example, if you have a list of data files from July 2019 that you wish to process in a folder in Blob Storage, below is a wildcard you can use in your Source transformation.

```DateFiles/*_201907*.txt```

By using wildcarding, your pipeline will only contain one Data Flow activity. This will perform better than a Lookup against the Blob Store that then iterates across all matched files using a ForEach with an Execute Data Flow activity inside.

## Next steps

See other Data Flow articles related to performance:

- [Data Flow Optimize Tab](concepts-data-flow-overview.md#optimize)
- [Data Flow activity](control-flow-execute-data-flow-activity.md)
- [Monitor Data Flow performance](concepts-data-flow-monitoring.md)
