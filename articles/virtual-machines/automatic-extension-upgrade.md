---
title: Automatic Extension Upgrade for VMs and Scale Sets in Azure
description: Learn how to enable the Automatic Extension Upgrade for your virtual machines and virtual machine scale sets in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar

---

# Preview: Automatic Extension Upgrade for VMs and Scale Sets in Azure

Automatic Extension Upgrade is available in preview for Azure VMs and Azure Virtual Machine Scale Sets. When Automatic Extension Upgrade is enabled on a VM or scale set, the extension is upgraded automatically whenever the extension publisher releases a new version for that extension.

 Automatic Extension Upgrade has the following features:
- Supported for Azure VMs and Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets are currently not supported.
- Upgrades are applied in an availability-first deployment model (detailed below).
- For a Virtual Machine Scale Set, no more than 20% of the scale set virtual machines will be upgraded in a single batch. The minimum batch size is one virtual machine.
- Works for all VM sizes, and for both Windows and Linux extensions.
- You can opt out of automatic upgrades at any time.
- Automatic extension upgrade can be enabled on a Virtual Machine Scale Sets of any size.
- Each supported extension is enrolled individually, and you can choose which extensions to upgrade automatically.
- Supported in all public cloud regions.


> [!IMPORTANT]
> Automatic Extension Upgrade is currently in Public Preview. An opt-in procedure is needed to use the public preview functionality described below.
> This preview version is provided without a service level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## How does Automatic Extension Upgrade work?
The extension upgrade process replaces the existing extension version on a VM with a new version of the same extension when published by the extension publisher. The health of the VM is monitored after the new extension is installed. If the VM is not in a healthy state within 5 minutes of the upgrade completion, the extension version is rolled back to the previous version.

A failed extension update is automatically retried. A retry is attempted every few days automatically without user intervention.

### Availability-first Updates
The availability-first model for platform orchestrated updates will ensure that availability configurations in Azure are respected across multiple availability levels.

For a group of virtual machines undergoing an update, the Azure platform will orchestrate updates:

**Across regions:**
- An update will move across Azure globally in a phased manner to prevent Azure-wide deployment failures.
- A 'phase' can have one or more regions, and an update moves across phases only if eligible VMs in the previous phase update successfully.
- Geo-paired regions will not be updated concurrently and cannot be in the same regional phase.
- The success of an update is measured by tracking the health of a VM post update. VM health is tracked through platform health indicators for the VM. For Virtual Machine Scale Sets, the VM health is tracked through application health probes or the Application Health extension, if applied to the scale set.

**Within a region:**
- VMs in different Availability Zones are not updated concurrently.
- Single VMs not part of an availability set are batched on a best effort basis to avoid concurrent updates for all VMs in a subscription.  

**Within a 'set':**
- All VMs in a common availability set or scale set are not updated concurrently.  
- VMs in a common availability set are updated within Update Domain boundaries and VMs across multiple Update Domains are not updated concurrently.  
- VMs in a common virtual machine scale set are grouped in batches and updated within Update Domain boundaries.

### Upgrade process for Virtual Machine Scale Sets
1. Before beginning the upgrade process, the orchestrator will ensure that no more than 20% of VMs in the entire scale set are unhealthy (for any reason).

2. The upgrade orchestrator identifies the batch of VM instances to upgrade. An upgrade batch can have a maximum of 20% of the total VM count, subject to a minimum batch size of one virtual machine.

3. For scale sets with configured application health probes or Application Health extension, the upgrade waits up to 5 minutes (or the defined health probe configuration) for the VM to become healthy before upgrading the next batch. If a VM does not recover its health after an upgrade, then by default the previous extension version on the VM is reinstalled.

4. The upgrade orchestrator also tracks the percentage of VMs that become unhealthy after an upgrade. The upgrade will stop if more than 20% of upgraded instances become unhealthy during the upgrade process.

The above process continues until all instances in the scale set have been upgraded.

The scale set upgrade orchestrator checks for the overall scale set health before upgrading every batch. While upgrading a batch, there could be other concurrent planned or unplanned maintenance activities that could impact the health of your scale set virtual machines. In such cases, if more than 20% of the scale set's instances become unhealthy, then the scale set upgrade stops at the end of current batch.

## Supported extensions
The preview of Automatic Extension Upgrade supports the following extensions (and more are added periodically):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) and [Linux](./extensions/agent-dependency-linux.md)
- [Application Health Extension](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows and Linux


## Enabling preview access
Enabling the preview functionality requires a one-time opt-in for the feature **AutomaticExtensionUpgradePreview** per subscription, as detailed below.

### REST API
The following example describes how to enable the preview for your subscription:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Feature registration can take up to 15 minutes. To check the registration status:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Once the feature has been registered for your subscription, complete the opt-in process by propagating the change into the Compute resource provider.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### Azure PowerShell
Use the [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet to enable the preview for your subscription.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Feature registration can take up to 15 minutes. To check the registration status:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Once the feature has been registered for your subscription, complete the opt-in process by propagating the change into the Compute resource provider.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### Azure CLI
Use [az feature register](/cli/azure/feature#az-feature-register) to enable the preview for your subscription.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Feature registration can take up to 15 minutes. To check the registration status:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Once the feature has been registered for your subscription, complete the opt-in process by propagating the change into the Compute resource provider.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## Enabling Automatic Extension Upgrade
To enable Automatic Extension Upgrade for an extension, you must ensure the property *enableAutomaticUpgrade* is set to *true* and added to every extension definition individually.


### REST API for Virtual Machines
To enable automatic extension upgrade for an extension (in this example the Dependency Agent extension) on an Azure VM, use the following:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### REST API for Virtual Machine Scale Sets
Use the following to add the extension to the scale set model:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
   	    "virtualMachineProfile": {
            "extensionProfile": {
       	        "extensions": [
            	{
                "name": "<extensionName>",
            	  "properties": {
             		    "autoUpgradeMinorVersion": true,
             		    "enableAutomaticUpgrade": true,
              	    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
              	    "type": "DependencyAgentWindows",
              	    "typeHandlerVersion": "9.5"
            		}
          	    }
        	    ]
    	    }
    	}
    }
}
```

### Azure PowerShell for Virtual Machines
Use the [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet:

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### Azure PowerShell for Virtual Machine Scale Sets
Use the [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet to add the extension to the scale set model:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Update the scale set using [Update-AzVmss](/powershell/module/az.compute/update-azvmss) after adding the extension.


### Azure CLI for Virtual Machines
Use the [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### Azure CLI for Virtual Machine Scale Sets
Use the [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) cmdlet to add the extension to the scale set model:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## Extension upgrades with multiple extensions

A VM or Virtual Machine Scale Set can have multiple extensions with automatic extension upgrade enabled. The same VM or scale set can also have other extensions without automatic extension upgrade enabled.  

If multiple extension upgrades are available for a virtual machine, the upgrades may be batched together, but each extension upgrade is applied individually on a virtual machine. A failure on one extension does not impact the other extension(s) that may be upgrading. For example, if two extensions are scheduled for an upgrade, and the first extension upgrade fails, the second extension will still be upgraded.

Automatic Extension Upgrades can also be applied when a VM or virtual machine scale set has multiple extensions configured with [extension sequencing](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Extension sequencing is applicable for the first-time deployment of the VM, and any future extension upgrades on an extension are applied independently.


## Next steps
> [!div class="nextstepaction"]
> [Learn about the Application Health Extension](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
