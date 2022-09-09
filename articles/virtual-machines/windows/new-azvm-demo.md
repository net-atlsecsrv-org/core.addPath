﻿---
title: Create Windows VM using simplified New-AzVM cmdlet in Azure Cloud Shell| Microsoft Docs
description: Quickly learn to create Windows virtual machines with the simplified New-AzVM cmdlet in Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX

---

# Create a Windows virtual machine with the simplified New-AzVM cmdlet in Cloud Shell 

The [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet has added a simplified set of parameters for creating a new VM using PowerShell. This topic shows you how to use PowerShell in Azure Cloud Shell, with the latest version of the New-AzureVM cmdlet preinstalled, to create a new VM. We will use a simplified parameter set that automatically creates all the necessary resources using smart defaults. 

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## Create the VM

You can use the [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet to create a VM with smart defaults that include using the Windows Server 2016 Datacenter image from the Azure Marketplace. You can use New-AzVM with just the **-Name** parameter and it will use that value for all of the resource names. In this example, we set the **-Name** parameter as *myVM*. 

Make sure that **PowerShell** is selected in Cloud Shell and type:

```azurepowershell-interactive
New-AzVm -Name myVM
```

You are asked to create a username and password for the VM, which will be used when you connect to the VM later in this topic. The password must be 12-123 characters long and meet three out of the four following complexity requirements: one lower case character, one upper case character, one number, and one special character.

It takes a minute to create the VM and the associated resources. When finished, you can see all of the resources that were created using the [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet.

```azurepowershell-interactive
Get-AzResource `
	-ResourceGroupName myVMResourceGroup | Format-Table Name
```

## Connect to the VM

After the deployment has completed, create a remote desktop connection with the virtual machine.

Use the [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) command to return the public IP address of the virtual machine. Take note of this IP Address.

```azurepowershell-interactive
Get-AzPublicIpAddress `
	-ResourceGroupName myVMResourceGroup | Select IpAddress
```

On your local machine, open a cmd prompt and use the **mstsc** command to start a remote desktop session with your new VM. Replace the &lt;publicIPAddress&gt; with the IP address of your virtual machine. When prompted, enter the username and password you gave your VM when it was created.

```
mstsc /v:<publicIpAddress>
```
## Specify different resource names

You can also provide more descriptive names for the resources, and still have them created automatically. Here is an example where we have named multiple resources for the new VM, including a new resource group.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## Clean up resources

When no longer needed, you can use the [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) command to remove the resource group, VM, and all related resources.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## Next steps

In this topic, you’ve deployed a simple virtual machine using New-AzVM and then connected to it over RDP. To learn more about Azure virtual machines, continue to the tutorial for Windows VMs.

> [!div class="nextstepaction"]
> [Azure Windows virtual machine tutorials](./tutorial-manage-vm.md)
