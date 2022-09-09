---
 title: include file
 description: include file
 services: storage
 author: tamram
 ms.service: storage
 ms.topic: include
 ms.date: 07/19/2019
 ms.author: tamram
 ms.custom: include file
---

The following table describes default limits for Azure general-purpose v1, v2, and Blob storage accounts. The *ingress* limit refers to all data from requests that are sent to a storage account. The *egress* limit refers to all data from responses that are received from a storage account.

| Resource | Default limit |
| --- | --- |
| Number of storage accounts per region per subscription, including both standard and premium accounts | 250 |
| Maximum storage account capacity | 2 PiB for US and Europe, and 500 TiB for all other regions (including the UK)<sup>1</sup>|
| Maximum number of blob containers, blobs, file shares, tables, queues, entities, or messages per storage account | No limit |
| Maximum request rate<sup>1</sup> per storage account | 20,000 requests per second |
| Maximum ingress<sup>1</sup> per storage account (US, Europe regions) | 25 Gbps |
| Maximum ingress<sup>1</sup> per storage account (regions other than US and Europe) | 5 Gbps if RA-GRS/GRS is enabled, 10 Gbps for LRS/ZRS<sup>2</sup> |
| Maximum egress for general-purpose v2 and Blob storage accounts (all regions) | 50 Gbps |
| Maximum egress for general-purpose v1 storage accounts (US regions) | 20 Gbps if RA-GRS/GRS is enabled, 30 Gbps for LRS/ZRS<sup>2</sup> |
| Maximum egress for general-purpose v1 storage accounts (non-US regions) | 10 Gbps if RA-GRS/GRS is enabled, 15 Gbps for LRS/ZRS<sup>2</sup> |

<sup>1</sup>Azure Standard Storage accounts support higher capacity limits and higher limits for ingress by request. To request an increase in account limits for ingress, contact [Azure Support](https://azure.microsoft.com/support/faq/). For more information, see [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>2</sup> If you have Read-access enabled (RA-GRS/RA-GZRS), the egress targets for the secondary location are identical to those of the primary location. [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy) options include:
[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> We recommend that you use a general-purpose v2 storage account for most scenarios. You can easily upgrade a general-purpose v1 or an Azure Blob storage account to a general-purpose v2 account with no downtime and without the need to copy data.
>
> For more information on Azure Storage accounts, see [Storage account overview](../articles/storage/common/storage-account-overview.md).

If the needs of your application exceed the scalability targets of a single storage account, you can build your application to use multiple storage accounts. You can then partition your data objects across those storage accounts. For information on volume pricing, see [Azure Storage pricing](https://azure.microsoft.com/pricing/details/storage/).

All storage accounts run on a flat network topology and support the scalability and performance targets outlined in this article, regardless of when they were created. For more information on the Azure Storage flat network architecture and on scalability, see [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

