---
title: azcopy env | Microsoft Docs
description: This article provides reference information for the azcopy env command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy env

Shows the environment variables that can configure AzCopy's behavior.

## Synopsis

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> If you set an environment variable by using the command line, that variable will be readable in your command line history. Consider clearing variables that contain credentials from your command line history. To keep variables from appearing in your history, you can use a script to prompt the user for their credentials, and to set the environment variable.

## Options

|Option|Description|
|--|--|
|-h, --help|Shows help content for the env command. |
|--show-sensitive|Shows sensitive/secret environment variables.|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
