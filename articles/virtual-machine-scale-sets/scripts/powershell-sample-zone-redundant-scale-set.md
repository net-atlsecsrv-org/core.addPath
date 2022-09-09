---
title: Azure PowerShell Samples - Zone-redundant scale set
description: This script creates a virtual machine scale set running Windows Server 2016 across multiple Availability Zones.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 04/05/2018
ms.reviewer: jushiman
ms.custom: mimckitt

---

# Create a zone-redundant virtual machine scale set with PowerShell
This script creates a virtual machine scale set running Windows Server 2016 across multiple Availability Zones. After running the script, you can access the virtual machine over RDP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## Sample script

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## Clean up deployment
Run the following command to remove the resource group, scale set, and all related resources.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## Script explanation
This script uses the following commands to create the deployment. Each item in the table links to command specific documentation.

| Command | Notes |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Creates the virtual machine scale set and all supporting resources, including virtual network, load balancer, and NAT rules. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Gets information on a virtual machine scale set. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Adds a VM extension for Custom Script to install a basic web application. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Updates the virtual machine scale set model to apply the VM extension. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Gets information on the public IP address assigned used by the load balancer. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Removes a resource group and all resources contained within. |


## Next steps
For more information on the Azure PowerShell module, see [Azure PowerShell documentation](/powershell/azure/).
