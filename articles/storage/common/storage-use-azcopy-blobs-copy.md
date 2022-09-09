---
title: Copy blobs between Azure storage accounts with AzCopy v10 | Microsoft Docs
description: This article contains a collection of AzCopy example commands that help you copy blobs between storage accounts.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
---

# Copy blobs between Azure storage accounts by using AzCopy v10

You can copy blobs, directories, and containers between storage accounts by using the AzCopy v10 command-line utility. 

To see examples for other types of tasks such as uploading files, downloading blobs, and synchronizing with Blob storage, see the links presented in the [Next Steps](#next-steps) section of this article.

AzCopy uses [server-to-server](/rest/api/storageservices/put-block-from-url) [APIs](/rest/api/storageservices/put-page-from-url), so data is copied directly between storage servers. These copy operations don't use the network bandwidth of your computer.

To download AzCopy and learn about the ways that you can provide authorization credentials to the storage service, see [Get started with AzCopy](storage-use-azcopy-v10.md). 

## Guidelines

Apply the following guidelines to your AzCopy commands. 

- Append a SAS token to each source URL. 

  If you provide authorization credentials by using Azure Active Directory (Azure AD), you can omit the SAS token only from the destination URL. Make sure that you've set up the proper roles in your destination account. See [Option 1: Use Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory). 

  The examples in this article assume that you've authenticated your identity by using Azure AD so the examples omit the SAS tokens from the destination URL.

-  If you copy to a premium block blob storage account, omit the access tier of a blob from the copy operation by setting the `s2s-preserve-access-tier` to `false` (For example: `--s2s-preserve-access-tier=false`). Premium block blob storage accounts don't support access tiers. 

- If you copy to or from an account that has a hierarchical namespace, use `blob.core.windows.net` instead of `dfs.core.windows.net` in the URL syntax. [Multi-protocol access on Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) enables you to use `blob.core.windows.net`, and it is the only supported syntax for account to account copy scenarios. 

- You can increase the throughput of copy operations by setting the value of the `AZCOPY_CONCURRENCY_VALUE` environment variable. To learn more, see [Optimize throughput](storage-use-azcopy-configure.md#optimize-throughput). 

- If the source blobs have index tags, and you want to retain those tags, you'll have to reapply them to the destination blobs. For information about how to set index tags, see the [Copy blobs to another storage account with index tags](#copy-between-accounts-and-add-index-tags) section of this article.

## Copy a blob

Copy a blob to another storage account by using the [azcopy copy](storage-ref-azcopy-copy.md) command. 

> [!TIP]
> This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Example** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

The copy operation is synchronous so when the command returns, that indicates that all files have been copied. 

## Copy a directory

Copy a directory to another storage account by using the [azcopy copy](storage-ref-azcopy-copy.md) command. 

> [!TIP]
> This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Example** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

The copy operation is synchronous so when the command returns, that indicates that all files have been copied.

## Copy a container

Copy a container to another storage account by using the [azcopy copy](storage-ref-azcopy-copy.md) command.

> [!TIP]
> This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Example** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

The copy operation is synchronous so when the command returns, that indicates that all files have been copied.

## Copy containers, directories, and blobs

Copy all containers, directories, and blobs to another storage account by using the [azcopy copy](storage-ref-azcopy-copy.md) command.

> [!TIP]
> This example encloses path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Example** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

The copy operation is synchronous so when the command returns, that indicates that all files have been copied.

<a id="copy-between-accounts-and-add-index-tags"></a>

## Copy blobs and add index tags

Copy blobs to another storage account and add [blob index tags(preview)](../blobs/storage-manage-find-blobs.md) to the target blob.

If you're using Azure AD authorization, your security principal must be assigned the [Storage Blob Data Owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) role or it must be given permission to the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure resource provider operation](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) via a custom Azure role. If you're using a Shared Access Signature (SAS) token, that token must provide access to the blob's tags via the `t` SAS permission.

To add tags, use the `--blob-tags` option along with a URL encoded key-value pair. 

For example, to add the key `my tag` and a value `my tag value`, you would add `--blob-tags='my%20tag=my%20tag%20value'` to the destination parameter. 

Separate multiple index tags by using an ampersand (`&`).  For example, if you want to add a key `my second tag` and a value `my second tag value`, the complete option string would be `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`.

The following examples show how to use the `--blob-tags` option.

> [!TIP]
> These examples enclose path arguments with single quotes (''). Use single quotes in all command shells except for the Windows Command Shell (cmd.exe). If you're using a Windows Command Shell (cmd.exe), enclose path arguments with double quotes ("") instead of single quotes ('').

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Directory** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Container** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Account** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

The copy operation is synchronous so when the command returns, that indicates that all files have been copied.

> [!NOTE]
> If you specify a directory, container, or account for the source, all the blobs that are copied to the destination will have the same tags that you specify in the command.

## Copy with optional flags

You can tweak your copy operation by using optional flags. Here's a few examples.

|Scenario|Flag|
|---|---|
|Copy blobs as Block, Page, or Append Blobs.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
|Copy to a specific access tier (such as the archive tier).|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
|Automatically decompress files.|**--decompress**=\[gzip\|deflate\]|

For a complete list, see [options](storage-ref-azcopy-copy.md#options). 

## Next steps

Find more examples in these articles:

- [Examples: Upload](storage-use-azcopy-blobs-upload.md)
- [Examples: Download](storage-use-azcopy-blobs-download.md)
- [Examples: Synchronize](storage-use-azcopy-blobs-synchronize.md)
- [Examples: Amazon S3 buckets](storage-use-azcopy-s3.md)
- [Examples: Azure Files](storage-use-azcopy-files.md)
- [Tutorial: Migrate on-premises data to cloud storage by using AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)