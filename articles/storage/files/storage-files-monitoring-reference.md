---
title: Azure Files monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Files.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
---

# Azure Files monitoring data reference

See [Monitoring Azure Files](storage-files-monitoring.md) for details on collecting and analyzing monitoring data for Azure Files.

## Metrics

The following tables list the platform metrics collected for Azure Files. 

### Capacity metrics

Capacity metrics values are refreshed daily (up to 24 Hours). The time grain defines the time interval for which metrics values are presented. The supported time grain for all capacity metrics is one hour (PT1H).

Azure Files provides the following capacity metrics in Azure Monitor.

#### Account Level

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### Azure Files

This table shows [Azure Files metrics](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

| Metric | Description |
| ------------------- | ----------------- |
| FileCapacity | The amount of File storage used by the storage account. <br/><br/> Unit: Bytes <br/> Aggregation Type: Average <br/> Value example: 1024 |
| FileCount   | The number of files in the storage account. <br/><br/> Unit: Count <br/> Aggregation Type: Average <br/> Value example: 1024 |
| FileShareCapacityQuota | The upper limit on the amount of storage that can be used by Azure Files Service in bytes. <br/><br/> Unit: Bytes <br/> Aggregation Type: Average <br/> Value example: 1024|
| FileShareCount | The number of file shares in the storage account. <br/><br/> Unit: Count <br/> Aggregation Type: Average <br/> Value example: 1024 |
| FileShareProvisionedIOPS | The number of provisioned IOPS on a file share. This metric is applicable to premium file storage only. <br/><br/> Unit: bytes <br/> Aggregation Type: Average |
| FileShareSnapshotCount | The number of snapshots present on the share in storage account's Azure Files service. <br/><br/> Unit:Count <br/> Aggregation Type: Average | 
|FileShareSnapshotSize|The amount of storage used by the snapshots in storage account's Azure Files service. <br/><br/> Unit: Bytes <br/> Aggregation Type: Average|

### Transaction metrics

Transaction metrics are emitted on every request to a storage account from Azure Storage to Azure Monitor. In the case of no activity on your storage account, there will be no data on transaction metrics in the period. All transaction metrics are available at both account and Azure Files service level. The time grain defines the time interval that metric values are presented. The supported time grains for all transaction metrics are PT1H and PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## Metrics dimensions

Azure Files supports following dimensions for metrics in Azure Monitor.

> [!NOTE] 
> The File Share dimension is not available for standard file shares (only premium file shares). When using standard file shares, the metrics provided are for all files shares in the storage account. To get per-share metrics for standard file shares, create one file share per storage account.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## Resource logs (preview)

> [!NOTE]
> Azure Storage logs in Azure Monitor is in public preview, and is available for preview testing in all public cloud regions. To enroll in the preview, see [this page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  This preview enables logs for blobs (including Azure Data Lake Storage Gen2), files, queues, tables, premium storage accounts in general-purpose v1 and general-purpose v2 storage accounts. Classic storage accounts are not supported.

The following table lists the properties for Azure Storage resource logs when they're collected in Azure Monitor Logs or Azure Storage. The properties describe the operation, the service, and the type of authorization that was used to perform the operation.

### Fields that describe the operation


[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### Fields that describe how the operation was authenticated

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### Fields that describe the service

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## See also

- See [Monitoring Azure Files](storage-files-monitoring-reference.md) for a description of monitoring Azure Storage.
- See [Monitoring Azure resources with Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.