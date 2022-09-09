---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 03/05/2020
 ms.author: rogarana
 ms.custom: include file
---

Incremental snapshots are point in time backups for managed disks that, when taken, consist only of all the changes since the last snapshot. When you attempt to download or otherwise use an incremental snapshot, the full VHD is used. This new capability for managed disk snapshots potentially allows them to be more cost effective, since, unless you choose to, you do not have to store the entire disk with each individual snapshot. Just like regular snapshots, incremental snapshots can be used to either create a full managed disk or to make a regular snapshot.

There are a few differences between an incremental snapshot and a regular snapshot. Incremental snapshots will always use standard HDDs storage, irrespective of the storage type of the disk, whereas regular snapshots can use premium SSDs. If you are using regular snapshots on Premium Storage to scale up VM deployments, we recommend you use custom images on standard storage in the [Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md). It will help you achieve a more massive scale with lower cost. Additionally, incremental snapshots potentially offer better reliability with [zone-redundant storage](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). If ZRS is available in the selected region, an incremental snapshot will use ZRS automatically. If ZRS is not available in the region, then the snapshot will default to [locally-redundant storage](../articles/storage/common/storage-redundancy-lrs.md) (LRS). You can override this behavior and select one manually but, we do not recommend that.

Incremental snapshots also offer a differential capability, only available to managed disks. They enable you to get the changes between two incremental snapshots of the same managed disks, down to the block level. You can use this capability to reduce your data footprint when copying snapshots across regions.
