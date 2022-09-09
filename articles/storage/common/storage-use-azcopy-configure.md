---
title: Configure, optimize, and troubleshoot AzCopy with Azure Storage | Microsoft Docs
description: Configure, optimize, and troubleshoot AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
---

# Configure, optimize, and troubleshoot AzCopy

AzCopy is a command-line utility that you can use to copy blobs or files to or from a storage account. This article helps you to perform advanced configuration tasks and helps you to troubleshoot issues that can arise as you use AzCopy.

> [!NOTE]
> If you're looking for content to help you get started with AzCopy, see any of the following articles:
> - [Get started with AzCopy](storage-use-azcopy-v10.md)
> - [Transfer data with AzCopy and blob storage](storage-use-azcopy-blobs.md)
> - [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)
> - [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)

## Configure proxy settings

To configure the proxy settings for AzCopy, set the `https_proxy` environment variable. If you run AzCopy on Windows, AzCopy automatically detects proxy settings, so you don't have to use this setting in Windows. If you choose to use this setting in Windows, it will override automatic detection.

| Operating system | Command  |
|--------|-----------|
| **Windows** | In a command prompt use: `set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell use: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Currently, AzCopy doesn't support proxies that require authentication with NTLM or Kerberos.

## Optimize performance

You can benchmark performance, and then use commands and environment variables to find an optimal tradeoff between performance and resource consumption.

### Run benchmark tests

You can run a performance benchmark test on specific blob containers to view general performance statistics and to identity performance bottlenecks. 

> [!NOTE]
> In the current release, this feature is available only for Blob Storage containers.

Use the following command to run a performance benchmark test.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Example** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

This command runs a performance benchmark by uploading test data to a specified destination. The test data is generated in memory, uploaded to the destination, then deleted from the destination after the test is complete. You can specify how many files to generate and what size you'd like them to be by using optional command parameters.

To view detailed help guidance for this command, type `azcopy bench -h` and then press the ENTER key.

### Optimize throughput

You can use the `cap-mbps` flag to place a ceiling on the throughput data rate. For example, the following command caps throughput to `10` megabits (MB) per second.

```azcopy
azcopy cap-mbps 10
```

Throughput can decrease when transferring small files. You can you can increase throughput by setting the `AZCOPY_CONCURRENCY_VALUE` environment variable. This variable specifies the number of concurrent requests that can occur.  

If your computer has fewer than 5 CPUs, then the value of this variable is set to `32`. Otherwise, the default value is equal to 16 multiplied by the number of CPUs. The maximum default value of this variable is `3000`, but you can manually set this value higher or lower. 

| Operating system | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use the `azcopy env` to check the current value of this variable. If the value is blank, then you can read which value is being used by looking at the beginning of any AzCopy log file. The selected value, and the reason it was selected, are reported there.

Before you set this variable, we recommend that you run a benchmark test. The benchmark test process will report the recommended concurrency value. Alternatively, if your network conditions and payloads vary, set this variable to the word `AUTO` instead of to a particular number. That will cause AzCopy to always run the same automatic tuning process that it uses in benchmark tests.

### Optimize memory use

Set the `AZCOPY_BUFFER_GB` environment variable to specify the maximum amount of your system memory you want AzCopy to use when downloading and uploading files.
Express this value in gigabytes (GB).

| Operating system | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## Troubleshoot issues

AzCopy creates log and plan files for every job. You can use the logs to investigate and troubleshoot any potential problems. 

The logs will contain the status of failure (`UPLOADFAILED`, `COPYFAILED`, and `DOWNLOADFAILED`), the full path, and the reason of the failure.

By default, the log and plan files are located in the `%USERPROFILE$\.azcopy` directory on Windows or `$HOME$\.azcopy` directory on Mac and Linux, but you can change that location if you want.

> [!IMPORTANT]
> When submitting a request to Microsoft Support (or troubleshooting the issue involving any third party), share the redacted version of the command you want to execute. This ensures the SAS isn't accidentally shared with anybody. You can find the redacted version at the start of the log file.

### Review the logs for errors

The following command will get all errors with `UPLOADFAILED` status from the `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### View and resume jobs

Each transfer operation will create an AzCopy job. Use the following command to view the history of jobs:

```
azcopy jobs list
```

To view the job statistics, use the following command:

```
azcopy jobs show <job-id>
```

To filter the transfers by status, use the following command:

```
azcopy jobs show <job-id> --with-status=Failed
```

Use the following command to resume a failed/canceled job. This command uses its identifier along with the SAS token as it isn't persistent for security reasons:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

When you resume a job, AzCopy looks at the job plan file. The plan file lists all the files that were identified for processing when the job was first created. When you resume a job, AzCopy will attempt to transfer all of the files that are listed in the plan file which weren't already transferred.

## Change the location of the plan and log files

By default, plan and log files are located in the `%USERPROFILE$\.azcopy` directory on Windows, or in the `$HOME$\.azcopy` directory on Mac and Linux. You can change this location.

### Change the location of plan files

Use any of these commands.

| Operating system | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Use the `azcopy env` to check the current value of this variable. If the value is blank, then plan files are written to the default location.

### Change the location of log files

Use any of these commands.

| Operating system | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Use the `azcopy env` to check the current value of this variable. If the value is blank, then logs are written to the default location.

## Change the default log level

By default, AzCopy log level is set to `INFO`. If you would like to reduce the log verbosity to save disk space, overwrite this setting by using the ``--log-level`` option. 

Available log levels are: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, and `FATAL`.

## Remove plan and log files

If you want to remove all plan and log files from your local machine to save disk space, use the `azcopy jobs clean` command.

To remove the plan and log files associated with only one job, use `azcopy jobs rm <job-id>`. Replace the `<job-id>` placeholder in this example with the job id of the job.


