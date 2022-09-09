---
title: azcopy make | Microsoft Docs
description: This article provides reference information for the azcopy make command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
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

## Examples

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## Options

|Option|Description|
|--|--|
|-h, --help|Show help content for the make command. |
|--quota-gb uint32|Specifies the maximum size of the share in gigabytes (GiB), 0 means you accept the file service's default quota.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
