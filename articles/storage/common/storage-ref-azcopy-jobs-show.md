---
title: azcopy jobs show | Microsoft Docs
description: This article provides reference information for the azcopy jobs show command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy jobs show

Shows detailed information for the given job ID.

## Synopsis

If only the job ID is supplied without a flag, then the progress summary of the job is returned.

The byte counts and percent complete that appears when you run this command reflect only files that are completed in the job. They don't reflect partially completed files.

If the `with-status` flag is set, then the list of transfers in the job with the given value will be shown.

```azcopy
azcopy jobs show [jobID] [flags]
```

## Related conceptual articles

- [Get started with AzCopy](storage-use-azcopy-v10.md)
- [Transfer data with AzCopy and Blob storage](storage-use-azcopy-blobs.md)
- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)
- [Configure, optimize, and troubleshoot AzCopy](storage-use-azcopy-configure.md)

## Options

|Option|Description|
|--|--|
|-h, --help|Shows help content for the show command.|
|--with-status string|Only list the transfers of job with this status, available values: Started, Success, Failed|

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy jobs](storage-ref-azcopy-jobs.md)
