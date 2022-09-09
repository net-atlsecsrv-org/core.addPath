---
title: 'Quickstart: Create a virtual network using a Resource Manager template'
titleSuffix: Azure Virtual Network
description: Learn how to use a Resource Manager template to create an Azure virtual network.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: 
---

# Quickstart: Create a virtual network - Resource Manager template

In this quickstart, you learn how to create a virtual network with two subnets using the Azure Resource Manager template. A virtual network is the fundamental building block for your private network in Azure. It enables Azure resources, like VMs, to securely communicate with each other and with the internet.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

You can also complete this quickstart using the [Azure portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md), or [Azure CLI](quick-create-cli.md).

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Review the template

The template used in this quickstart is from [Azure Quickstart templates](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

The following Azure resources have been defined in the template:
- [**Microsoft.Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks): create an Azure virtual network.
-  [**Microsoft.Network/virtualNetworks/subnets**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets) - create a subnet.

## Deploy the template

Deploy Resource Manager template to Azure:

1. Select **Deploy to Azure** to sign in to Azure and open the template. The template creates a virtual network with two subnets.

   [![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. In the portal, on the **Create a Virtual Network with two Subnets** page, type or select the following values:
   - **Resource group**: Select **Create new**, type a name for the resource group, and select **OK**.
   - **Virtual Network Name**: Type a name for the new virtual network.
3. Select **Review + create**, and then select **Create**.

## Review deployed resources

Explore the resources that were created with the virtual network.

To learn about the JSON syntax and properties for a virtual network in a template, see [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).

## Clean up resources

When you no longer need the resources that you created with the virtual network, delete the resource group. This removes the virtual network and all the related resources.

To delete the resource group, call the `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## Next steps
In this quickstart, you deployed an Azure virtual network with two subnets. To learn more about Azure virtual networks, continue to the tutorial for virtual networks.

> [!div class="nextstepaction"]
> [Filter network traffic](tutorial-filter-network-traffic.md)
