---
title: Manage resources that are created during the VM move process in Azure Resource Mover
description: Learn how to manage resources that are created during the VM move process in Azure Resource Mover
manager: evansma
author: rayne-wiselman 
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew

---
# Manage resources created for the VM move

This article describes how to manage resources that are created explicitly by [Azure Resource Mover](overview.md) to facilitate the VM move process. 

After moving VMs across regions, there are a number of resources created by Resource Mover that should be cleaned up manually.

## Delete resources created for VM move

Manually delete the move collection, and Site Recovery resources created for the VM move.

1. Review the resources in resource group ```ResourceMoverRG-<sourceregion>-<target-region>```.
2. Check that the VM and all other source resources in the move collection have been moved/deleted. This ensures that there are no pending resources using them.
2. Delete these resources.

    - The move collection name is ```movecollection-<sourceregion>-<target-region>```.
    - The cache storage account name is ```resmovecache<guid>```
    - The vault name is ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## Next steps

Try [moving a VM](tutorial-move-region-virtual-machines.md) to another region with Resource Mover.