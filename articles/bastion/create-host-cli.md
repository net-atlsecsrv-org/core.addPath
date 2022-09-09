---
title: 'Create a Bastion host using Azure CLI | Azure Bastion'
description: In this article, learn how to create and delete a Bastion host
services: bastion
author: cherylmc

ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
# Customer intent: As someone with a networking background, I want to create an Azure Bastion host.

---

# Create an Azure Bastion host using Azure CLI

This article shows you how to create an Azure Bastion host using Azure CLI. Once you provision the Azure Bastion service in your virtual network, the seamless RDP/SSH experience is available to all of the VMs in the same virtual network. Azure Bastion deployment is per virtual network, not per subscription/account or virtual machine.

Optionally, you can create an Azure Bastion host by using the [Azure portal](./tutorial-create-host-portal.md), or using [Azure PowerShell](bastion-create-host-powershell.md).

## Prerequisites

Verify that you have an Azure subscription. If you don't already have an Azure subscription, you can activate your [MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) or sign up for a [free account](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="createhost"></a>Create a bastion host

This section helps you create a new Azure Bastion resource using Azure CLI.

> [!NOTE]
> As shown in the examples, use the `--location` parameter with `--resource-group` for every command to ensure that the resources are deployed together.

1. Create a virtual network and an Azure Bastion subnet. You must create the Azure Bastion subnet using the name value **AzureBastionSubnet**. This value lets Azure know which subnet to deploy the Bastion resources to. This is different than a Gateway subnet. You must use a subnet of at least /27 or larger subnet (/27, /26, and so on). Create the **AzureBastionSubnet** without any route tables or delegations. If you use Network Security Groups on the **AzureBastionSubnet**, refer to the [Work with NSGs](bastion-nsg.md) article.

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Create a public IP address for Azure Bastion. The public IP is the public IP address the Bastion resource on which RDP/SSH will be accessed (over port 443). The public IP address must be in the same region as the Bastion resource you are creating.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Create a new Azure Bastion resource in the AzureBastionSubnet of your virtual network. It takes about 5 minutes for the Bastion resource to create and deploy.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## Next steps

* Read the [Bastion FAQ](bastion-faq.md) for additional information.
* To use Network Security Groups with the Azure Bastion subnet, see [Work with NSGs](bastion-nsg.md).
