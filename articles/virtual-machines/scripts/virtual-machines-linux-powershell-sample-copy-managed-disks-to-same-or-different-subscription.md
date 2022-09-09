---
title: Copy managed disks to subscription (Linux) - PowerShell sample
description: Azure PowerShell Script Sample - Copy (or move) managed disks to the same or a different subscription
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag

tags: azure-service-management

ms.assetid:
ms.service: virtual-machines-linux

ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
---

# Copy managed disks in the same subscription or different subscription with PowerShell (Linux)

This script creates a copy of an existing managed disk in the same subscription or different subscription. The new disk is created in the same region as the parent managed disk.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## Sample script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## Script explanation

This script uses following commands to create a new managed disk in the target subscription using the Id of the source managed disk. Each command in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Creates disk configuration that is used for disk creation. It includes the resource Id of the parent disk and location that is same as the location of parent disk.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Creates a disk using disk configuration, disk name, and resource group name passed as parameters. |

## Next steps

For more information on the Azure PowerShell module, see [Azure PowerShell documentation](/powershell/azure/).

Additional virtual machine PowerShell script samples can be found in the [Azure Linux VM documentation](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
