---
title: azcopy remove | Microsoft Docs
description: This article provides reference information for the azcopy remove command.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
---

# azcopy remove

Delete blobs or files from an Azure storage account.

## Synopsis

```azcopy
azcopy remove [resourceURL] [flags]
```

## Examples

Remove a single blob with SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remove an entire virtual directory with a SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remove only the top blobs inside a virtual directory but not its sub-directories:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remove a subset of blobs in a virtual directory (For example: only jpg and pdf files, or if the blob name is "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Remove an entire virtual directory, but exclude certain blobs from the scope (For example: every blob that starts with foo or ends with bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Remove specific blobs and virtual directories by putting their relative paths (NOT URL-encoded) in a file:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Remove a single file from a Blob Storage account that has a hierarchical namespace (include/exclude not supported).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remove a single directory a Blob Storage account that has a hierarchical namespace (include/exclude not supported):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## Options

**--exclude-path string**      Exclude these paths when removing. This option does not support wildcard characters (*). Checks relative path prefix. For example: myFolder;myFolder/subDirName/file.pdf.

**--exclude-pattern** string   Exclude files where the name matches the pattern list. For example: *.jpg;*.pdf;exactName

**-h, --help**                     help for remove

**--include-path** string      Include only these paths when removing. This option does not support wildcard characters (*). Checks relative path prefix. For example: myFolder;myFolder/subDirName/file.pdf

**--include-pattern** string   Include only files where the name matches the pattern list. For example: *.jpg;*.pdf;exactName

**--list-of-files** string     Defines the location of a file which contains the list of files and directories to be deleted. The relative paths should be delimited by line breaks, and the paths should NOT be URL-encoded.

**--log-level** string         Define the log verbosity for the log file. Available levels include: INFO(all requests/responses), WARNING(slow responses), ERROR(only failed requests), and NONE(no output logs). (default 'INFO') (default "INFO")

**--recursive**                Look into sub-directories recursively when syncing between directories.

## Options inherited from parent commands

|Option|Description|
|---|---|
|--cap-mbps uint32|Caps the transfer rate, in megabits per second. Moment-by-moment throughput might vary slightly from the cap. If this option is set to zero, or it is omitted, the throughput isn't capped.|
|--output-type string|Format of the command's output. The choices include: text, json. The default value is "text".|

## See also

- [azcopy](storage-ref-azcopy.md)
