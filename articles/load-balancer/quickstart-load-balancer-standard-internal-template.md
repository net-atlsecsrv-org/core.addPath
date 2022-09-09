---
title: Create an internal load balancer by using an Azure Resource Manager template (ARM template)
description: Learn how to create an internal Azure load balancer by using an Azure Resource Manager template (ARM template).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
---

# Quickstart: Create an internal load balancer to load balance VMs by using an ARM template

This quickstart describes how to use an Azure Resource Manager template (ARM template) to create an internal Azure load balancer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Review the template

The template used in this quickstart is from the [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Multiple Azure resources have been defined in the template:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Virtual machine storage accounts for boot diagnostics.
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): Availability set for virtual machines.
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Virtual network for load balancer and virtual machines.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): Network interfaces for virtual machines.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): Internal load balancer.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): Virtual machines.

To find more templates that are related to Azure Load Balancer, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## Deploy the template

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## Review deployed resources

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Select **Resource groups** from the left pane.

3. Select the resource group that you created in the previous section. The default resource group name is **myResourceGroupLB**

4. Verify the following resources were created in the resource group:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="User Azure portal to verify creation of resources." border="true":::

## Clean up resources

When no longer needed, you can use the [az group delete](/cli/azure/group#az-group-delete) command to remove the resource group and all resources contained within.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## Next steps

For a step-by-step tutorial that guides you through the process of creating a template, see:

> [!div class="nextstepaction"]
> [Tutorial: Create and deploy your first ARM template](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)