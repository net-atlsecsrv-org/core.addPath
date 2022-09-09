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

- Only one virtual network can be linked to a disk access object.
- Your virtual network must be in the same subscription as your disk access object to link them.
- Up to 10 disks or snapshots can be imported or exported at the same time with the same disk access object.
- You cannot request manual approval to link a virtual network to a disk access object.
- The differential capability is not supported for incremental snapshots that are associated with a disk access object.