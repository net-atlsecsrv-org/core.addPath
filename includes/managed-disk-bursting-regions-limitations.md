---
 title: include file
 description: include file
 services: virtual-machines
 author: albecker1
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 01/04/2021
 ms.author: albecker1
 ms.custom: include file
---
- On-demand bursting cannot be enabled on a premium SSD that has less than 512 GiB. Premium SSDs less than 512 GiB will always use credit-based bursting.
- On-demand bursting is only supported on premium SSDs. If a premium SSD with on-demand bursting enabled is switched to another disk type, then disk bursting is disabled.
- On-demand bursting doesn't automatically disable itself when the performance tier is changed. If you want to change your performance tier but do not want to keep disk bursting, you must disable it.
- On-demand bursting can only be enabled when the disk is detached from a VM or when the VM is stopped. On-demand bursting can be disabled 12 hours after it has been enabled.