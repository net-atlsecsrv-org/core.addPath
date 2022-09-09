---
title: azcopy list | Microsoft Docs
description: This article provides reference information for the azcopy list command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy list

Lists the entities in a given resource.

## Synopsis

Only Blob containers are supported in the current release.

```azcopy
azcopy list [containerURL] [flags]
```

## Examples

```azcopy
azcopy list [containerURL]
```

## Options

|Option|Description|
|--|--|
|-h, --help|Show help content for the list command.|
|--machine-readable|Lists file sizes in bytes.|
|--mega-units|Displays units in orders of 1000, not 1024.|
|--running-tally|Counts the total number of files and their sizes.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
