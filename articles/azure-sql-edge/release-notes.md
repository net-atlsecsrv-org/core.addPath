---
title: Release notes for Azure SQL Edge 
description: Release notes detailing what's new or what has changed in the Azure SQL Edge images. 
keywords: release notes SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice:
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
---
# Azure SQL Edge release notes 

This article describes what's new and what has changed with every new build of Azure SQL Edge.

## Azure SQL Edge 1.0.1

SQL engine build 15.0.2000.1553

### What's new?

- Allow Date_Bucket expressions defined in computed columns.

### Fixes

- Retention policy fix for dropping a table that has a retention policy enabled with an infinite timeout
- DacFx deployment support for streaming features and retention-policy features 
- DacFx deployment fix to enable deployment from a nested folder in a SAS URL 
- PREDICT fix to support long column names in error messages

## Azure SQL Edge 1.0.0 (RTM)

SQL engine build 15.0.2000.1552

### What's new?
- Container images based on Ubuntu 18.04 
- Support for `IGNORE NULL` and `RESPECT NULL` syntax with `LAST_VALUE()` and `FIRST_VALUE()` functions 
- Reliability improvements for PREDICT with ONNX
- Support for cleanup based on the data-retention policy:
   - Ring buffer support for a retention cleanup task for troubleshooting
- New feature support: 
   - Fast recovery
   - Automatic tuning of queries
   - Parallel-execution scenarios
- Power-saving improvements for low-power mode
- Streaming new-feature support: 
   - [Snapshot windows](/stream-analytics-query/snapshot-window-azure-stream-analytics): A new window type allows you to group by events that arrive at the same time.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) and [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) can be enabled as analytic functions. You can return records ordered by the column of your choice. They don't need to be part of a window. 
   - Improvements to [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### Fixes
- Additional error messages and details for troubleshooting T-SQL streaming operations 
- Improvements to preserve battery life in idle mode 
- T-SQL streaming engine fixes: 
   - Cleanup for stopped streaming jobs 
   - Fixes for localization 
   - Improved Unicode handling 
   - Improved debugging for SQL Edge T-SQL streaming, allowing users to query job-failure errors from get_streaming_job
- Cleanup based on data-retention policy: 
    - Fixes for retention-policy creation and cleanup scenarios
- Fixes for background timer tasks to improve power savings for low-power mode

### Known issues 
- The Date_Bucket T-SQL function can't be used in a computed column.


## CTP 2.3
SQL engine build 15.0.2000.1549
### What's new?
- Support for custom origins in the Date_Bucket() function 
- Support for BACPAC files as part of SQL deployment
- Support for cleanup based on the data-retention policy:      
   - DDL support for enabling the retention policy 
   - Cleanup for stored procedures and the background cleanup task
   - Extended events to monitor cleanup tasks

### Fixes
- Additional error messages and details for troubleshooting T-SQL streaming operations 
- Improvements to preserve battery life in idle mode 
- T-SQL streaming engine: 
   - Fix for stuck watermark in the substreamed hopping window 
   - Fix for framework exception handling to make sure it's collected as a user-actionable error


## CTP 2.2
SQL engine build 15.0.2000.1546
### What's new?
- Support for nonroot containers 
- Support for the usage and diagnostic data collection 
- T-SQL streaming updates:
   - Support for Unicode characters for stream object names

### Fixes
- T-SQL streaming updates:
   - Process cleanup improvements
   - Logging and diagnostics improvements
- Performance improvement for data ingestion

## CTP 2.1 
SQL engine build 15.0.2000.1545
### Fixes
- Allowed the PREDICT-with-ONNX models to handle a CPUID issue in ARM 
- Improved handling of the failure path when T-SQL streaming starts
- Corrected value of the watermark delay in job metrics when there's no data.
- Fix for an issue with the output adapter when the adapter has a variable schema between batches  

## CTP 2.0 
SQL engine build 15.0.2000.1401
### What's new?
- 	Product name updated to *Azure SQL Edge*
-  Date_Bucket function:
    - Support for Date, Time, and DateTime types
- PREDICT with ONNX:
    - ONNX requirement for the RUNTIME parameter  
- T-SQL streaming support (limited preview) 
 
### Known issues

- Issue: Potential failures with applying DACPAC on startup because of a timing issue.
- Workaround: Restart SQL Server. Otherwise, the container will retry applying the DACPAC.

### Request support
You can request support on the [support page](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Select the following fields: 
- **Issue type**: *Technical* 
- **Service**: *IoT Edge*
- **Problem type**: *My problem relates to an IoT Edge module*
- **Problem subtype**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Screenshot showing a sample support ticket.":::

## CTP 1.5
SQL engine build 15.0.2000.1331
### What's new?
- Date_Bucket function:
    - Support for the DateTimeOffset type
- PREDICT with ONNX models:
  - NVARCHAR support
 
## CTP 1.4
SQL engine build 15.0.2000.1247
### What's new?
-	PREDICT with ONNX models:
    - VARCHAR support
    - Migration to ONNX runtime version 1.0 

- The following features are enabled:
  - CDC support
  - History table with compression
  - A higher-scale factor for log read-ahead
  - Batch mode ES filter pushdown
  - Read-ahead optimizations
 
## CTP 1.3
SQL engine build 15.0.2000.1147
### What's new?
- Azure IoT portal deployment: 
    - Support for deploying AMD64 and ARM images
    - Support for streaming job creation
    - DACPAC deployment
- PREDICT with ONNX models:
    - Numeric type support
- The following features are enabled:
    - Pushdown aggregate to column store scan
    - Merry-go-round scans
- Footprint and memory-consumption reduction work
