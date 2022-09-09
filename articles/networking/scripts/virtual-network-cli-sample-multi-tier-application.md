---
title: Azure CLI script sample - Create a network for multi-tier applications | Microsoft Docs
description: Azure CLI script sample - Create a virtual network for multi-tier applications.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm:
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud

---

# Create a network for multi-tier applications

This script sample creates a virtual network with front-end and back-end subnets. Traffic to the front-end subnet is limited to HTTP and SSH, while traffic to the back-end subnet is limited to MySQL, port 3306. After running the script, you will have two virtual machines, one in each subnet that you can deploy web server and MySQL software to.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## Sample script


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## Clean up deployment 

Run the following command to remove the resource group, VM, and all related resources.

```azurecli
az group delete --name MyResourceGroup --yes
```

## Script explanation

This script uses the following commands to create a resource group, virtual network,  and network security groups. Each command in the table links to command-specific documentation.

| Command | Notes |
|---|---|
| [az group create](/cli/azure/group) | Creates a resource group in which all resources are stored. |
| [az network vnet create](/cli/azure/network/vnet) | Creates an Azure virtual network and front-end subnet. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Creates a back-end subnet. |
| [az network public-ip create](/cli/azure/network/public-ip) | Creates a public IP address to access the VM from the Internet. |
| [az network nic create](/cli/azure/network/nic) | Creates virtual network interfaces and attaches them to the virtual network's front-end and back-end subnets. |
| [az network nsg create](/cli/azure/network/nsg) | Creates network security groups (NSG) that are associated to the front-end and back-end subnets. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Creates NSG rules that allow or block specific ports to specific subnets. |
| [az vm create](/cli/azure/vm) | Creates virtual machines and attaches a NIC to each VM. This command also specifies the virtual machine image to use and administrative credentials. |
| [az group delete](/cli/azure/group) | Deletes a resource group and all resources it contains. |

## Next steps

For more information on the Azure CLI, see [Azure CLI documentation](/cli/azure).

Additional networking CLI script samples can be found in the [Azure Networking Overview documentation](../cli-samples.md)
