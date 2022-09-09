---
title: Known issues with Azure Data Lake Storage Gen2 | Microsoft Docs
description: Learn about limitations and known issues of Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: normesta
ms.reviewer: jamesbak
---
# Known issues with Azure Data Lake Storage Gen2

This article describes limitations and known issues of Azure Data Lake Storage Gen2.

## Supported Blob storage features

An increasing number of Blob storage features now work with accounts that have a hierarchical namespace. For a complete list, see [Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## Supported Azure service integrations

Azure Data Lake Storage Gen2 supports several Azure services that you can use to ingest data, perform analytics, and create visual representations. For a list of supported Azure services, see [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

See [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## Supported open source platforms

Several open source platforms support Data Lake Storage Gen2. For a complete list, see [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

See [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## Blob storage APIs

Blob APIs and Data Lake Storage Gen2 APIs can operate on the same data.

This section describes issues and limitations with using blob APIs and Data Lake Storage Gen2 APIs to operate on the same data.

* You cannot use blob API and Data Lake Storage APIs to write to the same instance of a file. If you write to a file by using Data Lake Storage Gen2 APIs, then that file's blocks won't be visible to calls to the [Get Block List](/rest/api/storageservices/get-block-list) blob API. The only exception is when using you are overwriting. You can overwrite a file/blob using either API.

* When you use the [List Blobs](/rest/api/storageservices/list-blobs) operation without specifying a delimiter, the results will include both directories and blobs. If you choose to use a delimiter, use only a forward slash (`/`). This is the only supported delimiter.

* If you use the [Delete Blob](/rest/api/storageservices/delete-blob) API to delete a directory, that directory will be deleted only if it's empty. This means that you can't use the Blob API delete directories recursively.

These Blob REST APIs aren't supported:

* [Put Blob (Page)](/rest/api/storageservices/put-blob)
* [Put Page](/rest/api/storageservices/put-page)
* [Get Page Ranges](/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](/rest/api/storageservices/put-page-from-url)
* [Append Block](/rest/api/storageservices/append-block)
* [Append Block from URL](/rest/api/storageservices/append-block-from-url)


Unmanaged VM disks are not supported in accounts that have a hierarchical namespace. If you want to enable a hierarchical namespace on a storage account, place unmanaged VM disks into a storage account that doesn't have the hierarchical namespace feature enabled.

<a id="api-scope-data-lake-client-library"></a>

## Support for setting access control lists (ACLs) recursively

The ability to apply ACL changes recursively from parent directory to child items is generally available. In the current release of this capability, you can apply ACL changes by using PowerShell, Azure CLI, and the .NET, Java, and Python SDK. Support is not yet available for the Azure portal, or Azure Storage Explorer.

<a id="known-issues-tools"></a>

## AzCopy

Use only the latest version of AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Earlier versions of AzCopy such as AzCopy v8.1, are not supported.

<a id="storage-explorer"></a>

## Azure Storage Explorer

Use only versions `1.6.0` or higher.

<a id="explorer-in-portal"></a>

## Storage Explorer in the Azure portal

ACLs are not yet supported.

<a id="third-party-apps"></a>

## Third party applications

Third party applications that use REST APIs to work will continue to work if you use them with Data Lake Storage Gen2
Applications that call Blob APIs will likely work.

## Access control lists (ACL) and anonymous read access

If [anonymous read access](./anonymous-read-access-configure.md) has been granted to a container, then ACLs have no effect on that container or the files in that container.

## Diagnostic logs

The setting for retention days is not yet supported, but you can delete logs manually by using any supported tool such as Azure Storage Explorer, REST or an SDK.

## Lifecycle management policies with premium tier for Azure Data Lake Storage

You can't move data that's stored in the premium tier between hot, cool, and archive tiers. However, you can copy data from the premium tier to the hot access tier in a different account.

## Dremio support with premium-performance BlockBlobStorage storage accounts

Dremio doesn't yet connect to a BlockBlobStorage account that has the hierarchical namespace feature enabled on it. 

## Windows Azure Storage Blob (WASB) driver (unsupported with Data Lake Storage Gen2)

Currently, the WASB driver, which was designed to work with the Blob API only, encounters problems in a few common scenarios. Specifically, when it is a client to a hierarchical namespace-enabled storage account. Multi-protocol access on Data Lake Storage won't mitigate these issues. 

For the time being (and most likely the foreseeable future), we won't support customers using the WASB driver as a client to a hierarchical namespace-enabled storage account. Instead, we recommend that you opt to use the [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) driver in your Hadoop environment. If you are trying to migrate off of an on-premise Hadoop environment with a version earlier than Hadoop branch-3, then please open an Azure Support ticket so that we can get in touch with you on the right path forward for you and your organization.