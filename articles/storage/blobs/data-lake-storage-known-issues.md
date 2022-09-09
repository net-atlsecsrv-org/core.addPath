---
title: Known issues with Azure Data Lake Storage Gen2 | Microsoft Docs
description: Learn about limitations and known issues of Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
---
# Known issues with Azure Data Lake Storage Gen2

This article describes limitations and known issues of Azure Data Lake Storage Gen2.

## Supported Blob storage features

An increasing number of Blob storage features now work with accounts that have a hierarchical namespace. For a complete list, see [Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## Supported Azure service integrations

Data Lake Storage gen2 supports several Azure services that you can use to ingest data, perform analytics, and create visual representations. For a list of supported Azure services, see [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

See [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## Supported open source platforms

Several open source platforms support Data Lake Storage Gen2. For a complete list, see [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

See [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## Blob storage APIs

Blob APIs and Data Lake Storage Gen2 APIs can operate on the same data.

This section describes issues and limitations with using blob APIs and Data Lake Storage Gen2 APIs to operate on the same data.

* You can't use both Blob APIs and Data Lake Storage APIs to write to the same instance of a file. If you write to a file by using Data Lake Storage Gen2 APIs, then that file's blocks won't be visible to calls to the [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API. You can overwrite a file by using either Data Lake Storage Gen2 APIs or Blob APIs. This won't affect file properties.

* When you use the [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) operation without specifying a delimiter, the results will include both directories and blobs. If you choose to use a delimiter, use only a forward slash (`/`). This is the only supported delimiter.

* If you use the [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API to delete a directory, that directory will be deleted only if it's empty. This means that you can't use the Blob API delete directories recursively.

These Blob REST APIs aren't supported:

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Unmanaged VM disks are not supported in accounts that have a hierarchical namespace. If you want to enable a hierarchical namespace on a storage account, place unmanaged VM disks into a storage account that doesn't have the hierarchical namespace feature enabled.

<a id="api-scope-data-lake-client-library" />

## File system support in SDKs

- [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) and [Python](data-lake-storage-directory-file-acl-python.md), and [JavaScript](data-lake-storage-directory-file-acl-javascript.md) and support are in public preview. Other SDKs are not currently supported.
- Get and set ACL operations are not currently recursive.

## File system support in PowerShell and Azure CLI

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) and [Azure CLI](data-lake-storage-directory-file-acl-cli.md) support are in public preview.
- Get and set ACL operations are not currently recursive.

## Lifecycle management policies

* The deletion of blob snapshots is not yet supported.  

* There are currently some bugs affecting lifecycle management policies and the archive access tier. 

## Diagnostic logs

Azure Storage Explorer 1.10.x can't be used for viewing diagnostic logs. To view logs, please use AzCopy or SDKs.

## Blobfuse

Blobfuse is not supported.



<a id="known-issues-tools" />

## AzCopy

Use only the latest version of AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Earlier versions of AzCopy such as AzCopy v8.1, are not supported.

<a id="storage-explorer" />

## Azure Storage Explorer

Use only versions `1.6.0` or higher.There is currently a storage bug affecting version `1.11.0` that can result in authentication errors in certain scenarios. A fix for the storage bug is being rolled out, but as a workaround, we recommend that you use version `1.10.x` which is available as a [free download](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` is not affected by the storage bug.

<a id="explorer-in-portal" />

## Storage Explorer in the Azure portal

ACLs are not yet supported.

<a id="third-party-apps" />

## Third party applications

Third party applications that use REST APIs to work will continue to work if you use them with Data Lake Storage Gen2
Applications that call Blob APIs will likely work.

## Access control lists (ACL) and anonymous read access

If [anonymous read access](storage-manage-access-to-resources.md) has been granted to a container, then ACLs have no effect on that container or the files in that container.

## Windows Azure Storage Blob (WASB) driver

Currently, there are several issues associated with using the WASB driver along with accounts that have a hierarchical namespace. We recommend that you use the [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) driver in your workloads. 





