---
title: InfiniBand driver extension - Azure Linux VMs 
description: Microsoft Azure Extension for installing InfiniBand Drivers on H- and N-series compute VMs running Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid:
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma

---

# InfiniBand Driver Extension for Linux

This extension installs InfiniBand OFED drivers on InfiniBand and SR-IOV-enabled ('r' sizes) [H-series](../sizes-hpc.md) and [N-series](../sizes-gpu.md) VMs running Linux. Depending on the VM family, the extension installs the appropriate drivers for the Connect-X NIC.

Instructions on manual installation of the OFED drivers are available [here](../workloads/hpc/enable-infiniband.md#manual-installation).

An extension is also available to install InfiniBand drivers for [Windows VMs](hpc-compute-infiniband-windows.md).

## Prerequisites

### Operating system

This extension supports the following OS distros, depending on driver support for specific OS version.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS, 20.04 LTS |
| Linux: CentOS | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |

### Internet connectivity

The Microsoft Azure Extension for InfiniBand Drivers requires that the target VM is connected to and has access to the internet.

## Extension schema

The following JSON shows the schema for the extension.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### Properties

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1.1 | int |



## Deployment


### Azure Resource Manager Template 

Azure VM extensions can be deployed with Azure Resource Manager templates. Templates are ideal when deploying one or more virtual machines that require post deployment configuration.

The JSON configuration for a virtual machine extension can be nested inside the virtual machine resource, or placed at the root or top level of a Resource Manager JSON template. The placement of the JSON configuration affects the value of the resource name and type. For more information, see [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md). 

The following example assumes the extension is nested inside the virtual machine resource. When nesting the extension resource, the JSON is placed in the `"resources": []` object of the virtual machine.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
	}'
```

### Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### Add extension to a Virtual Machine Scale Set

The following example installs the latest version 1.1 InfiniBandDriverLinux extension on all RDMA-capable VMs in an existing virtual machine scale set named *myVMSS* deployed in the resource group named *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## Troubleshoot and support

### Troubleshoot

Data about the state of extension deployments can be retrieved from the Azure portal, and by using Azure PowerShell and Azure CLI. To see the deployment state of extensions for a given VM, run the following command.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Extension execution output is logged to the following file. Refer to this file to track the status of the installation as well as for troubleshooting any failures.

```bash
/var/log/azure/ib-vmext-status
```

### Exit codes

The following table describes the meaning and recommended action based on the exit codes of the extension installation process.

| Exit Code | Meaning | Possible Action |
| :---: | --- | --- |
| 0 | Operation successful |
| 1 | Incorrect usage of extension | Check execution output log |
| 10 | Linux Integration Services for Hyper-V and Azure not available or installed | Check output of lspci |
| 11 | Mellanox InfiniBand not found on this VM size | Use a [supported VM size and OS](../sizes-hpc.md) |
| 12 | Image offer not supported |
| 13 | VM size not supported | Use an InfiniBand-enabled ('r' size) [H-series](../sizes-hpc.md) and [N-series](../sizes-gpu.md)N-series VM to deploy |
| 14 | Operation unsuccessful | Check execution output log |


### Support

If you need more help at any point in this article, you can contact the Azure experts on the [MSDN Azure and Stack Overflow forums](https://azure.microsoft.com/support/community/). Alternatively, you can file a support incident through the [Azure support site](https://azure.microsoft.com/support/options/). For information about using Azure Support, read the [Microsoft Azure support FAQ](https://azure.microsoft.com/support/faq/).

## Next steps
For more information about InfiniBand-enabled ('r' sizes), see [H-series](../sizes-hpc.md) and [N-series](../sizes-gpu.md) VMs.

> [!div class="nextstepaction"]
> [Learn more about Linux VMs extensions and features](features-linux.md)