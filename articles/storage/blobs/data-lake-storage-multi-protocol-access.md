---
title: Multi-protocol access on Azure Data Lake Storage (preview) | Microsoft Docs
description: Use Blob APIs and applications that use Blob APIs with Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: normesta
ms.reviewer: stewu
---
# Multi-protocol access on Azure Data Lake Storage (preview)

Blob APIs now work with accounts that have a hierarchical namespace. This unlocks the entire ecosystem of tools, applications, and services, as well as all Blob storage features to accounts that have a hierarchical namespace.

Until recently, you might have had to maintain separate storage solutions for object storage and analytics storage. That's because Azure Data Lake Storage Gen2 had limited ecosystem support. It also had limited access to Blob service features such as diagnostic logging. A fragmented storage solution is hard to maintain because you have to move data between accounts to accomplish various scenarios. You no longer have to do that.

> [!NOTE]
> Multi-protocol access on Data Lake Storage is in public preview, and is available in [these regions](#region-availability). To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article. To enroll in the preview, see [this page](https://aka.ms/blobinteropsignup).

## Use the entire ecosystem of applications, tools, and services

If you enroll in the preview of multi-protocol access on Data Lake Storage, you can work with all of your data by using the entire ecosystem of tools, applications, and services. This includes Azure services such as [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction), [IOT Hub](https://docs.microsoft.com/azure/iot-hub/), [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources), and many others. 

This also includes third-party tools and applications. You can point them to accounts that have a hierarchical namespace without having to modify them. These applications work *as is* even if they call Blob APIs, because Blob APIs can now operate on data in accounts that have a hierarchical namespace.

> [!NOTE]
> To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article.

## Use all Blob storage features

Blob storage features such as [diagnostic logging](../common/storage-analytics-logging.md), [access tiers](storage-blob-storage-tiers.md), and [Blob storage lifecycle management policies](storage-lifecycle-management-concepts.md) now work with accounts that have a hierarchical namespace. Therefore, you can enable hierarchical namespaces on your blob storage accounts without loosing access to these important features. 

> [!NOTE]
> To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article.

## How multi-protocol access on data lake storage works

Blob APIs and Data Lake Storage Gen2 APIs can operate on the same data in storage accounts that have a hierarchical namespace. Data Lake Storage Gen2 routes Blob APIs through the hierarchical namespace so that you can get the benefits of first class directory operations and POSIX-compliant access control lists (ACLs). 

![Multi-protocol access on Data Lake Storage conceptual](./media/data-lake-storage-interop/interop-concept.png) 

Existing tools and applications that use the Blob API gain these benefits automatically. Developers won't have to modify them. Data Lake Storage Gen2 consistently applies directory and file-level ACLs regardless of the protocol that tools and applications use to access the data. 

<a id="region-availability" />

## Region availability

Multi-protocol access on Azure Data Lake Storage (preview) is available in the following regions:

|||||
|-|-|-|-|
|Central US|West Central US|Canada Central|
|East US|East Asia|North Europe|
|East US 2|Southeast Asia|West Europe|
|West US|Australia East|Japan East|
|West US 2|Brazil South||

## Next steps

See [Known issues](data-lake-storage-known-issues.md)




