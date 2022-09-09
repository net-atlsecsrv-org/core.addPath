---
title: azcopy logout | Microsoft Docs
description: This article provides reference information for the azcopy logout command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy logout

Logs the user out and terminates access to Azure Storage resources.

## Synopsis

This command will remove all the cached login information for the current user.

```azcopy
azcopy logout [flags]
```

## Options

|Option|Description|
|--|--|
|-h, --help|Show help content for the logout command.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
