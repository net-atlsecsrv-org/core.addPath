---
title: Azure Data Lake Storage Gen2 Introduction
description: Read an introduction to Azure Data Lake Storage Gen2. Learn key features. Review supported Blob storage features, Azure service integrations, and platforms.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
---

# Introduction to Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 is a set of capabilities dedicated to big data analytics, built on [Azure Blob storage](storage-blobs-introduction.md). 

Data Lake Storage Gen2 converges the capabilities of [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) with Azure Blob storage. For example, Data Lake Storage Gen2 provides file system semantics, file-level security, and scale. Because these capabilities are built on Blob storage, you'll also get low-cost, tiered storage, with high availability/disaster recovery capabilities.

## Designed for enterprise big data analytics

Data Lake Storage Gen2 makes Azure Storage the foundation for building enterprise data lakes on Azure. Designed from the start to service multiple petabytes of information while sustaining hundreds of gigabits of throughput, Data Lake Storage Gen2 allows you to easily manage massive amounts of data.

A fundamental part of Data Lake Storage Gen2 is the addition of a [hierarchical namespace](data-lake-storage-namespace.md) to Blob storage. The hierarchical namespace organizes objects/files into a hierarchy of directories for efficient data access. A common object store naming convention uses slashes in the name to mimic a hierarchical directory structure. This structure becomes real with Data Lake Storage Gen2. Operations such as renaming or deleting a directory, become single atomic metadata operations on the directory. There's no need to enumerate and process all objects that share the name prefix of the directory.

Data Lake Storage Gen2 builds on Blob storage and enhances performance, management, and security in the following ways:

-   **Performance** is optimized because you do not need to copy or transform data as a prerequisite for analysis. Compared to the flat namespace on Blob storage, the hierarchical namespace greatly improves the performance of directory management operations, which improves overall job performance.

-   **Management** is easier because you can organize and manipulate files through directories and subdirectories.

-   **Security** is enforceable because you can define POSIX permissions on directories or individual files.

Also, Data Lake Storage Gen2 is very cost effective because it is built on top of the low-cost [Azure Blob storage](storage-blobs-introduction.md). The additional features further lower the total cost of ownership for running big data analytics on Azure.

## Key features of Data Lake Storage Gen2

-   **Hadoop compatible access**: Data Lake Storage Gen2 allows you to manage and access data just as you would with a [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). The new [ABFS driver](data-lake-storage-abfs-driver.md) (used to access data) is available within all Apache Hadoop environments. These environments include [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), and [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics).

-   **A superset of POSIX permissions**: The security model for Data Lake Gen2 supports ACL and POSIX permissions along with some extra granularity specific to Data Lake Storage Gen2. Settings may be configured through Storage Explorer or through frameworks like Hive and Spark.

-   **Cost effective**: Data Lake Storage Gen2 offers low-cost storage capacity and transactions. Features such as [Azure Blob storage lifecycle](storage-lifecycle-management-concepts.md) optimize costs as data transitions through its lifecycle.

-   **Optimized driver**: The ABFS driver is [optimized specifically](data-lake-storage-abfs-driver.md) for big data analytics. The corresponding REST APIs are surfaced through the endpoint `dfs.core.windows.net`.

### Scalability

Azure Storage is scalable by design whether you access via Data Lake Storage Gen2 or Blob storage interfaces. It is able to store and serve *many exabytes of data*. This amount of storage is available with throughput measured in gigabits per second (Gbps) at high levels of input/output operations per second (IOPS). Processing is executed at near-constant per-request latencies that are measured at the service, account, and file levels.

### Cost effectiveness

Because Data Lake Storage Gen2 is built on top of Azure Blob storage, storage capacity and transaction costs are lower. Unlike other cloud storage services, you don't have to move or transform your data before you can analyze it. For more information about pricing, see [Azure Storage pricing](https://azure.microsoft.com/pricing/details/storage).

Additionally, features such as the [hierarchical namespace](data-lake-storage-namespace.md) significantly improve the overall performance of many analytics jobs. This improvement in performance means that you require less compute power to process the same amount of data, resulting in a lower total cost of ownership (TCO) for the end-to-end analytics job.

### One service, multiple concepts

Because Data Lake Storage Gen2 is built on top of Azure Blob storage, multiple concepts can describe the same, shared things.

The following are the equivalent entities, as described by different concepts. Unless specified otherwise these entities are directly synonymous:

| Concept                                | Top Level Organization | Lower Level Organization                                            | Data Container |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – General purpose object storage | Container              | Virtual directory (SDK only – does not provide atomic manipulation) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics Storage          | Container            | Directory                                                           | File           |

## Supported Blob storage features

Blob storage features such as [diagnostic logging](../common/storage-analytics-logging.md), [access tiers](storage-blob-storage-tiers.md), and [Blob Storage lifecycle management policies](storage-lifecycle-management-concepts.md) are available to your account. 

For a list of supported Blob storage features, see [Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## Supported Azure service integrations

Data Lake Storage gen2 supports several Azure services. You can use them to ingest data, perform analytics, and create visual representations. For a list of supported Azure services, see [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## Supported open source platforms

Several open source platforms support Data Lake Storage Gen2. For a complete list, see [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## See also

- [Known issues with Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Multi-protocol access on Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


