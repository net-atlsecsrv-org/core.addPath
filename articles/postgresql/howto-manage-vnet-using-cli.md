---
title: Use virtual network rules - Azure CLI - Azure Database for PostgreSQL - Single Server
description: This article describes how to create and manage VNet service endpoints and rules for Azure Database for PostgreSQL using Azure CLI command line.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019 
ms.custom: devx-track-azurecli
---
# Create and manage VNet service endpoints for Azure Database for PostgreSQL - Single Server using Azure CLI
Virtual Network (VNet) services endpoints and rules extend the private address space of a Virtual Network to your Azure Database for PostgreSQL server. Using convenient Azure Command Line Interface (CLI) commands, you can create, update, delete, list, and show VNet service endpoints and rules to manage your server. For an overview of Azure Database for PostgreSQL VNet service endpoints, including limitations, see [Azure Database for PostgreSQL Server VNet service endpoints](concepts-data-access-and-security-vnet.md). VNet service endpoints are available in all supported regions for Azure Database for PostgreSQL.

## Prerequisites
To step through this how-to guide, you need:
- Install [the Azure CLI](/cli/azure/install-azure-cli) or use the Azure Cloud Shell in the browser.
- An [Azure Database for PostgreSQL server and database](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Support for VNet service endpoints is only for General Purpose and Memory Optimized servers.
> In case of VNet peering, if traffic is flowing through a common VNet Gateway with service endpoints and is supposed to flow to the peer, please create an ACL/VNet rule to allow Azure Virtual Machines in the Gateway VNet to access the Azure Database for PostgreSQL server.


## Configure Vnet service endpoints for Azure Database for PostgreSQL
The [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) commands are used to configure Virtual Networks.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this article requires that you are running the Azure CLI version 2.0 or later. To see the version installed, run the `az --version` command. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). 

If you are running the CLI locally, you need to log in to your account using the [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) command. Note the **id** property from the command output for the corresponding subscription name.
```azurecli-interactive
az login
```

If you have multiple subscriptions, choose the appropriate subscription in which the resource should be billed. Select the specific subscription ID under your account using [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) command. Substitute the **id** property from the **az login** output for your subscription into the subscription id placeholder.

- The account must have the necessary permissions to create a virtual network and service endpoint.

Service endpoints can be configured on virtual networks independently, by a user with write access to the virtual network.

To secure Azure service resources to a VNet, the user must have permission to "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" for the subnets being added. This permission is included in the built-in service administrator roles, by default and can be modified by creating custom roles.

Learn more about [built-in roles](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) and assigning specific permissions to [custom roles](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

VNets and Azure service resources can be in the same or different subscriptions. If the VNet and Azure service resources are in different subscriptions, the resources should be under the same Active Directory (AD) tenant. Ensure that both the subscriptions have the **Microsoft.Sql** resource provider registered. For more information refer [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> It is highly recommended to read this article about service endpoint configurations and considerations before running the sample script below, or configuring service endpoints. **Virtual Network service endpoint:** A [Virtual Network service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) is a subnet whose property values include one or more formal Azure service type names. VNet services endpoints use the service type name **Microsoft.Sql**, which refers to the Azure service named SQL Database. This service tag also applies to the Azure SQL Database, Azure Database for PostgreSQL and MySQL services. It is important to note when applying the **Microsoft.Sql** service tag to a VNet service endpoint it configures service endpoint traffic for all Azure Database services, including Azure SQL Database, Azure Database for PostgreSQL and Azure Database for MySQL servers on the subnet. 
> 

### Sample script to create an Azure Database for PostgreSQL database, create a VNet, VNet service endpoint and secure the server to the subnet with a VNet rule
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## Clean up deployment
After the script sample has been run, the following command can be used to remove the resource group and all resources associated with it.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
