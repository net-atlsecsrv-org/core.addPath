---
title: Data redundancy in Azure Storage | Microsoft Docs
description: Data in your Microsoft Azure Storage account is replicated for durability and high availability. Redundancy options include locally redundant storage (LRS), zone-redundant storage (ZRS), geo-redundant storage (GRS), read-access geo-redundant storage (RA-GRS), geo-zone-redundant storage (GZRS) (preview), and read-access geo-zone-redundant storage (RA-GZRS) (preview).
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
---

# Azure Storage redundancy

The data in your Microsoft Azure storage account is always replicated to ensure durability and high availability. Azure Storage copies your data so that it is protected from planned and unplanned events, including transient hardware failures, network or power outages, and massive natural disasters. You can choose to replicate your data within the same data center, across zonal data centers within the same region, or across geographically separated regions.

Replication ensures that your storage account meets the [Service-Level Agreement (SLA) for Storage](https://azure.microsoft.com/support/legal/sla/storage/) even in the face of failures. See the SLA for information about Azure Storage guarantees for durability and availability.

Azure Storage regularly verifies the integrity of data stored using cyclic redundancy checks (CRCs). If data corruption is detected, it is repaired using redundant data. Azure Storage also calculates checksums on all network traffic to detect corruption of data packets when storing or retrieving data.

## Choosing a redundancy option

When you create a storage account, you can select one of the following redundancy options:

* [Locally redundant storage (LRS)](storage-redundancy-lrs.md)
* [Zone-redundant storage (ZRS)](storage-redundancy-zrs.md)
* [Geo-redundant storage (GRS)](storage-redundancy-grs.md)
* [Read-access geo-redundant storage (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [Geo-zone-redundant storage (GZRS)](storage-redundancy-gzrs.md)
* [Read-access geo-zone-redundant storage (RA-GZRS)](storage-redundancy-gzrs.md)

The following table provides a quick overview of the scope of durability and availability that each replication strategy will provide you for a given type of event (or event of similar impact).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Node unavailability within a data center                                                                 | Yes                             | Yes                              | Yes                                  | Yes                                  |
| An entire data center (zonal or non-zonal) becomes unavailable                                           | No                              | Yes                              | Yes                                  | Yes                                  |
| A region-wide outage                                                                                     | No                              | No                               | Yes                                  | Yes                                  |
| Read access to your data (in a remote, geo-replicated region) in the event of region-wide unavailability | No                              | No                               | Yes (with RA-GRS)                                   | Yes (with RA-GZRS)                                 |
| Designed to provide \_\_ durability of objects over a given year                                          | at least 99.999999999% (11 9's) | at least 99.9999999999% (12 9's) | at least 99.99999999999999% (16 9's) | at least 99.99999999999999% (16 9's) |
| Supported storage account types                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2                     |
| Availability SLA for read requests | At least 99.9% (99% for cool access tier) | At least 99.9% (99% for cool access tier) | At least 99.9% (99% for cool access tier) | At least 99.99% (99.9% for Cool Access Tier) |
| Availability SLA for write requests | At least 99.9% (99% for cool access tier) | At least 99.9% (99% for cool access tier) | At least 99.9% (99% for cool access tier) | At least 99.9% (99% for cool access tier) |

All data in your storage account is replicated, including block blobs and append blobs, page blobs, queues, tables, and files. All types of storage accounts are replicated, although ZRS requires a general-purpose v2 storage account.

For pricing information for each redundancy option, see [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/). 

For information about Azure Storage guarantees for durability and availability, see the [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Azure Premium Storage supports only locally redundant storage (LRS).

## Changing replication strategy

You can change your storage account's replication strategy by using the [Azure portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), or one of the [Azure Storage client libraries](https://docs.microsoft.com/azure/index#pivot=sdkstools). Changing the replication type of your storage account does not result in down time.

   > [!NOTE]
   > Currently, you cannot use the Azure portal or the Azure Storage client libraries to convert your account to ZRS, GZRS, or RA-GZRS. To migrate your account to ZRS, see [Zone-redundant storage (ZRS) for building highly available Azure Storage applications](storage-redundancy-zrs.md) for details. To migrate GZRS or RA-GZRS, see [Geo-zone-redundant storage for highly availability and maximum durability (preview)](storage-redundancy-zrs.md) for details.
    
### Are there any costs to changing my account's replication strategy?

It depends on your conversion path. Ordering from least to the most expensive, Azure Storage redundancy offerings LRS, ZRS, GRS, RA-GRS, GZRS, and RA-GZRS. For example, going *from* LRS to any other type of replication will incur additional charges because you are moving to a more sophisticated redundancy level. Migrating *to* GRS or RA-GRS will incur an egress bandwidth charge because your data (in your primary region) is being replicated to your remote secondary region. This charge is a one-time cost at initial setup. After the data is copied, there are no further migration charges. You are only charged for replicating any new or updates to existing data. For details on bandwidth charges, see [Azure Storage Pricing page](https://azure.microsoft.com/pricing/details/storage/blobs/).

If you migrate your storage account from GRS to LRS, there is no additional cost, but your replicated data is deleted from the secondary location.

If you migrate your storage account from RA-GRS to GRS or LRS, that account is billed as RA-GRS for an additional 30 days beyond the date that it was converted.

## See also

- [Locally redundant storage (LRS): Low-cost data redundancy for Azure Storage](storage-redundancy-lrs.md)
- [Zone-redundant storage (ZRS): Highly available Azure Storage applications](storage-redundancy-zrs.md)
- [Geo-redundant storage (GRS): Cross-regional replication for Azure Storage](storage-redundancy-grs.md)
- [Geo-zone-redundant storage (GZRS) for highly availability and maximum durability (preview)](storage-redundancy-gzrs.md)
- [Azure Storage scalability and performance targets](storage-scalability-targets.md)
- [Designing highly available applications using RA-GRS Storage](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundancy options and read access geo redundant storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: A highly available cloud storage service with strong consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
