---
title: Managed disk bursting
description: Learn about disk bursting for Azure disks and Azure virtual machines.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
---
# Managed disk bursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## Virtual Machine level bursting
VM level bursting is enabled on the following VM series in all regions that they are supported in:
- [Lsv2-series](lsv2-series.md)
- [Dsv3-series](dv3-dsv3-series.md)
- [Esv3-series](ev3-esv3-series.md)

Bursting is enabled by default for virtual machines that support it.

## Disk level bursting
Bursting is also available on our [premium SSDs](disks-types.md#premium-ssd) for disk sizes P20 and smaller in all regions in Azure Public, Government, and China Clouds. Disk bursting is enabled by default on all new and existing deployments of the disk sizes that support it. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## Next steps

To learn how to gain insight into your bursting resources, see [Disk bursting metrics](disks-metrics.md).
