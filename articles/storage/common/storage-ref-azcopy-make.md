---
title: azcopy make | Microsoft Docs
description: This article provides reference information for the azcopy make command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy make

Creates a container or file share.

## Synopsis

Create a container or file share represented by the given resource URL.

```azcopy
azcopy make [resourceURL] [flags]
```

## Related conceptual articles

- [Get started with AzCopy](storage-use-azcopy-v10.md)
- [Transfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)

## Examples

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## Options

|Option|Description|
|--|--|
|-h, --help|Show help content for the make command. |
|--quota-gb uint32|Specifies the maximum size of the share in gigabytes (GB), zero means you accept the file service's default quota.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps float|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|
|--trusted-microsoft-suffixes string   |Specifies additional domain suffixes where Azure Active Directory login tokens may be sent.  The default is '*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net'. Any listed here are added to the default. For security, you should only put Microsoft Azure domains here. Separate multiple entries with semi-colons.|

## See also

- [azcopy](storage-ref-azcopy.md)
