---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 08/26/2020
 ms.author: rogarana
 ms.custom: include file
---

Enabling shared disks is only available to a subset of disk types. Currently only ultra disks and premium SSDs can enable shared disks. Each managed disk that have shared disks enabled are subject to the following limitations, organized by disk type:

### Ultra disks

Ultra disks have their own separate list of limitations, unrelated to shared disks. For ultra disk limitations, refer to [Using Azure ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

When sharing ultra disks, they have the following additional limitations:

- Currently limited to Azure Resource Manager or SDK support. 
- Only basic disks can be used with some versions of Windows Server Failover Cluster, for details see [Failover clustering hardware requirements and storage options](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Shared ultra disks are available in all regions that support ultra disks by default, and do not require you to sign up for access to use them.

### Premium SSDs

- Currently only supported in [a subset of regions](#regional-availability).
- Currently limited to Azure Resource Manager or SDK support. 
- Can only be enabled on data disks, not OS disks.
- **ReadOnly** host caching is not available for premium SSDs with `maxShares>1`.
- Disk bursting is not available for premium SSDs with `maxShares>1`.
- When using Availability sets and virtual machine scale sets with Azure shared disks, [storage fault domain alignment](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) with virtual machine fault domain is not enforced for the shared data disk.
- When using [proximity placement groups (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md), all virtual machines sharing a disk must be part of the same PPG.
- Only basic disks can be used with some versions of Windows Server Failover Cluster, for details see [Failover clustering hardware requirements and storage options](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Azure Backup and Azure Site Recovery support is not yet available.

#### Regional availability

Shared premium SSDs are only supported in the following regions:

- East US
- East US 2
- West US
- West US 2
- West Central US
- South Central US
- North Central US
- Central US
- West Europe
- North Europe
- Korea Central
- Canada Central
- Canada East
- Japan East
- Japan West
- US Gov Virginia
- US Gov Arizona

If you're interested in trying shared premium SSDs, [sign up for access](https://aka.ms/AzureSharedDiskGASignUp).