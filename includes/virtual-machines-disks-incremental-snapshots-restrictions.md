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

- Incremental snapshots currently cannot be moved between subscriptions.
- You can currently only generate SAS URIs of up to five snapshots of a particular snapshot family at any given time.
- You cannot create an incremental snapshot for a particular disk outside of that disk's subscription.
- Up to seven incremental snapshots per disk can be created every five minutes.
- A total of 200 incremental snapshots can be created for a single disk.
- You cannot get the changes between snapshots taken before and after you changed the size of the parent disk across 4 TB boundary. For example, You took an incremental snapshot snapshot-a when the size of a disk was 2 TB. Now you increased the size of the disk to 6 TB and then took another incremental snapshot snapshot-b. You cannot get the changes between snapshot-a and snapshot-b. You have to again download the full copy of snapshot-b created after the resize. Subsequently, you can get the changes between snapshot-b and  snapshots created after snapshot-b. 
