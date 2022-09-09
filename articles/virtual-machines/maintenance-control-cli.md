---
title: Maintenance control for Azure virtual machines
description: Learn how to control when maintenace is applied to your Azure VMs using Maintenance Control.
services: virtual-machines-linux
author: cynthn

ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
---

# Preview: Control updates with Maintenance Control and the Azure CLI

Manage platform updates, that don't require a reboot, using maintenance control. Azure frequently updates its infrastructure to improve reliability, performance, security or launch new features. Most updates are transparent to users. Some sensitive workloads, like gaming, media streaming, and financial transactions, can’t tolerate even few seconds of a VM freezing or disconnecting for maintenance. Maintenance control gives you the option to wait on platform updates and apply them within a 35-day rolling window. 

Maintenance control lets you decide when to apply updates to your isolated VMs and Azure Dedicated Hosts.

With maintenance control, you can:
- Batch updates into one update package.
- Wait up to 35 days to apply updates. 
- Automate platform updates for your maintenance window using Azure Functions.
- Maintenance configurations work across subscriptions and resource groups. 

> [!IMPORTANT]
> Maintenance Control is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## Limitations

- VMs must be on a [dedicated host](./linux/dedicated-hosts.md), or be created using an [isolated VM size](./linux/isolation.md).
- After 35 days, an update will automatically be applied.
- User must have **Resource Owner** access.


## Install the maintenance extension

If you choose to install the [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) locally, you need version 2.0.76 or later.

Install the `maintenance` preview CLI extension locally or in Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## Create a maintenance configuration

Use `az maintenance configuration create` to create a maintenance configuration. This example creates a maintenance configuration named *myConfig* scoped to the host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Copy the configuration ID from the output to use later.

Using `--maintenanceScope host` ensures that the maintenance config is used for controlling updates to the host.

If you try to create a configuration with the same name, but in a different location, you will get an error. Configuration names must be unique to your subscription.

You can query for available maintenance configurations using `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## Assign the configuration

Use `az maintenance assignment create` to assign the configuration to your isolated VM or Azure Dedicated Host.

### Isolated VM

Apply the configuration to a VM using the ID of the configuration. Specify `--resource-type virtualMachines` and supply the name of the VM for `--resource-name`, and the resource group for to the VM in `--resource-group`, and the location of the VM for `--location`. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id '/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig'
```

### Dedicated host

To apply a configuration to a dedicated host, you need to include `--resource-type hosts`, `--resource-parent-name` with the name of the host group, and `--resource-parent-type hostGroups`. 

The parameter `--resource-id` is the ID of the host. You can use [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) to get the ID of your dedicated host.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## Check configuration

You can verify that the configuration was applied correctly, or check to see what configuration is currently applied using `az maintenance assignment list`.

### Isolated VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### Dedicated host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## Check for pending updates

Use `az maintenance update list` to see if there are pending updates. Update --subscription to be the ID for the subscription that contains the VM.

### Isolated VM

Check for pending updates for an isolated VM. In this example, the output is formatted as a table for readability.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### Dedicated host

To check for pending updates for a dedicated host. In this example, the output is formatted as a table for readability. Replace the values for the resources with your own.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## Apply updates

Use `az maintenance apply update` to apply pending updates.

### Isolated VM

Create a request to apply updates to an isolated VM.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myMaintenanceRG\
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### Dedicated host

Apply updates to a dedicated host.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## Check the status of applying updates 

You can check on the progress of the updates using `az maintenance applyupdate get`. 

### Isolated VM

Replace `myUpdateName` with the name of the update that was returned when you ran `az maintenance applyupdate create`.

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name myUpdateName 
```

### Dedicated host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name default \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## Delete a maintenance configuration

Use `az maintenance configuration delete` to delete a maintenance configuration. Deleting the configuration removes the maintenance control from the associated resources.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## Next steps
To learn more, see [Maintenance and updates](maintenance-and-updates.md).