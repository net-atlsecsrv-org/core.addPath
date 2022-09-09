---
title: Configure virtual network based access for an Azure Cosmos account
description: This document describes the steps required to set up a virtual network service endpoint for Azure Cosmos DB. 
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown 
ms.custom: devx-track-azurecli

---

# Configure access to Azure Cosmos DB from virtual networks (VNet)

You can configure the Azure Cosmos account to allow access only from a specific subnet of virtual network (VNet). By enabling [Service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) to access Azure Cosmos DB on the subnet within a virtual network, the traffic from that subnet is sent to Azure Cosmos DB with the identity of the subnet and Virtual Network. Once the Azure Cosmos DB service endpoint is enabled, you can limit access to the subnet by adding it to your Azure Cosmos account.

By default, an Azure Cosmos account is accessible from any source if the request is accompanied by a valid authorization token. When you add one or more subnets within VNets, only requests originating from those subnets will get a valid response. Requests originating from any other source will receive a 403 (Forbidden) response. 

You can configure Azure Cosmos DB accounts to allow access from only a specific subnet of an Azure virtual network. To limit access to an Azure Cosmos DB account with connections from a subnet in a virtual network:

1. Enable the subnet to send the subnet and virtual network identity to Azure Cosmos DB. You can achieve this by enabling a service endpoint for Azure Cosmos DB on the specific subnet.

1. Add a rule in the Azure Cosmos DB account to specify the subnet as a source from which the account can be accessed.

> [!NOTE]
> When a service endpoint for your Azure Cosmos DB account is enabled on a subnet, the source of the traffic that reaches Azure Cosmos DB switches from a public IP to a virtual network and subnet. The traffic switching applies for any Azure Cosmos DB account that's accessed from this subnet. If your Azure Cosmos DB accounts have an IP-based firewall to allow this subnet, requests from the service-enabled subnet no longer match the IP firewall rules, and they're rejected.
>
> To learn more, see the steps outlined in the [Migrating from an IP firewall rule to a virtual network access control list](#migrate-from-firewall-to-vnet) section of this article.

The following sections describe how to configure a virtual network service endpoint for an Azure Cosmos DB account.

## <a id="configure-using-portal"></a>Configure a service endpoint by using the Azure portal

### Configure a service endpoint for an existing Azure virtual network and subnet

1. From the **All resources** blade, find the Azure Cosmos DB account that you want to secure.

1. Select **Firewalls and virtual networks** from the settings menu, and choose to allow access from **Selected networks**.

1. To grant access to an existing virtual network's subnet, under **Virtual networks**, select **Add existing Azure virtual network**.

1. Select the **Subscription** from which you want to add an Azure virtual network. Select the Azure **Virtual networks** and **Subnets** that you want to provide access to your Azure Cosmos DB account. Next, select **Enable** to enable selected networks with service endpoints for "Microsoft.AzureCosmosDB". When it's complete, select **Add**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Select virtual network and subnet":::

1. After the Azure Cosmos DB account is enabled for access from a virtual network, it will allow traffic from only this chosen subnet. The virtual network and subnet that you added should appear as shown in the following screenshot:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Virtual network and subnet configured successfully":::

> [!NOTE]
> To enable virtual network service endpoints, you need the following subscription permissions:
>   * Subscription with virtual network: Network contributor
>   * Subscription with Azure Cosmos DB account: DocumentDB account contributor
>   * If your virtual network and Azure Cosmos DB account are in different subscriptions, make sure that the subscription that has virtual network also has `Microsoft.DocumentDB` resource provider registered. To register a resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md) article.

Here are the directions for registering subscription with resource provider.

### Configure a service endpoint for a new Azure virtual network and subnet

1. From the **All resources** blade, find the Azure Cosmos DB account that you want to secure.  

1. Select **Firewalls and Azure virtual networks** from the settings menu, and choose to allow access from **Selected networks**.  

1. To grant access to a new Azure virtual network, under **Virtual networks**, select **Add new virtual network**.  

1. Provide the details required to create a new virtual network, and then select **Create**. The subnet will be created with a service endpoint for "Microsoft.AzureCosmosDB" enabled.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Select a virtual network and subnet for a new virtual network":::

If your Azure Cosmos DB account is used by other Azure services like Azure Cognitive Search, or is accessed from Stream analytics or Power BI, you allow access by selecting **Accept connections from within global Azure datacenters**.

To ensure that you have access to Azure Cosmos DB metrics from the portal, you need to enable **Allow access from Azure portal** options. To learn more about these options, see the [Configure an IP firewall](how-to-configure-firewall.md) article. After you enable access, select **Save** to save the settings.

## <a id="remove-vnet-or-subnet"></a>Remove a virtual network or subnet

1. From the **All resources** blade, find the Azure Cosmos DB account for which you assigned service endpoints.  

1. Select **Firewalls and virtual networks** from the settings menu.  

1. To remove a virtual network or subnet rule, select **...** next to the virtual network or subnet, and select **Remove**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Remove a virtual network":::

1. Select **Save** to apply your changes.

## <a id="configure-using-powershell"></a>Configure a service endpoint by using Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Use the following steps to configure a service endpoint to an Azure Cosmos DB account by using Azure PowerShell:  

1. Install [Azure PowerShell](/powershell/azure/install-Az-ps) and [sign in](/powershell/azure/authenticate-azureps).  

1. Enable the service endpoint for an existing subnet of a virtual network.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > When you're using PowerShell or the Azure CLI, be sure to specify the complete list of IP filters and virtual network ACLs in parameters, not just the ones that need to be added.

1. Get virtual network information.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Prepare a Cosmos DB Virtual Network Rule

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Update Azure Cosmos DB account properties with the new Virtual Network endpoint configuration: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Run the following command to verify that your Azure Cosmos DB account is updated with the virtual network service endpoint that you configured in the previous step:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a id="configure-using-cli"></a>Configure a service endpoint by using the Azure CLI

Azure Cosmos accounts can be configured for service endpoints when they are created or updated at a later time if the subnet is already configured for them. Service endpoints can also be enabled on the Cosmos account where the subnet is not yet configured for them and then will begin to work when the subnet is configured later. This flexibility allows for administrators who do not have access to both the Cosmos account and virtual network resources to make their configurations independent of each other.

### Create a new Cosmos account and connect it to a back end subnet for a new virtual network

In this example the virtual network and subnet is created with service endpoints enabled for both when they are created.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### Connect and configure a Cosmos account to a back end subnet independently

This sample is intended to show how to connect an Azure Cosmos account to an existing new virtual network where the subnet is not yet configured for service endpoints. This is done  by using the `--ignore-missing-vnet-service-endpoint` parameter. This allows the configuration for the Cosmos account to complete without error before the configuration to the virtual network's subnet is complete. Once the subnet configuration is complete, the Cosmos account will then be accessible through the configured subnet.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## Port range when using direct mode

When you're using service endpoints with an Azure Cosmos account through a direct mode connection, you need to ensure that the TCP port range from 10000 to 20000 is open.

## <a id="migrate-from-firewall-to-vnet"></a>Migrating from an IP firewall rule to a virtual network ACL

To migrate an Azure Cosmos DB account from using IP firewall rules to using virtual network service endpoints, use the following steps.

After an Azure Cosmos DB account is configured for a service endpoint for a subnet, requests from that subnet are sent to Azure Cosmos DB with virtual network and subnet source information instead of a source public IP address. These requests will no longer match an IP filter configured on the Azure Cosmos DB account, which is why the following steps are necessary to avoid downtime.

Before proceeding, enable the Azure Cosmos DB service endpoint on the virtual network and subnet using the step shown above in "Enable the service endpoint for an existing subnet of a virtual network".

1. Get virtual network and subnet information:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Prepare a new Virtual Network rule object for the Azure Cosmos DB account:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Update the Azure Cosmos DB account to enable service endpoint access from the subnet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Repeat the previous steps for all Azure Cosmos DB accounts accessed from the subnet.

1. Remove the IP firewall rule for the subnet from the Azure Cosmos DB account's Firewall rules.

## Frequently asked questions

Here are some frequently asked questions about configuring access from virtual networks:

### Are Notebooks and Mongo/Cassandra Shell currently compatible with Virtual Network enabled accounts?

At the moment the [Mongo shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) and [Cassandra shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) integrations in the Cosmos DB Data Explorer, and the [Jupyter Notebooks service](./cosmosdb-jupyter-notebooks.md), are not supported with VNET access. This is currently in active development.

### Can I specify both virtual network service endpoint and IP access control policy on an Azure Cosmos account? 

You can enable both the virtual network service endpoint and an IP access control policy (aka firewall) on your Azure Cosmos account. These two features are complementary and collectively ensure isolation and security of your Azure Cosmos account. Using IP firewall ensures that static IPs can access your account. 

### How do I limit access to subnet within a virtual network? 

There are two steps required to limit access to Azure Cosmos account from a subnet. First, you allow traffic from subnet to carry its subnet and virtual network identity to Azure Cosmos DB. This is done by enabling service endpoint for Azure Cosmos DB on the subnet. Next is adding a rule in the Azure Cosmos account specifying this subnet as a source from which account can be accessed.

### Will virtual network ACLs and IP Firewall reject requests or connections? 

When IP firewall or virtual network access rules are added, only requests from allowed sources get valid responses. Other requests are rejected with a 403 (Forbidden). It is important to distinguish Azure Cosmos account's firewall from a connection level firewall. The source can still connect to the service and the connections themselves aren't rejected.

### My requests started getting blocked when I enabled service endpoint to Azure Cosmos DB on the subnet. What happened?

Once service endpoint for Azure Cosmos DB is enabled on a subnet, the source of the traffic reaching the account switches from public IP to virtual network and subnet. If your Azure Cosmos account has IP-based firewall only, traffic from service enabled subnet would no longer match the IP firewall rules and therefore be rejected. Go over the steps to seamlessly migrate from IP-based firewall to virtual network-based access control.

### Are additional RBAC permissions needed for Azure Cosmos accounts with VNET service endpoints?

After you add the VNet service endpoints to an Azure Cosmos account, to make any changes to the account settings, you need access to the `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` action for all the VNETs configured on your Azure Cosmos account. This permission is required because the authorization process validates access to resources (such as database and virtual network resources) before evaluating any properties.
 
The authorization validates permission for VNet resource action even if the user doesn't specify the VNET ACLs using Azure CLI. Currently, the Azure Cosmos account's control plane supports setting the complete state of the Azure Cosmos account. One of the parameters to the control plane calls is `virtualNetworkRules`. If this parameter is not specified, the Azure CLI makes a get database call to retrieves the `virtualNetworkRules` and uses this value in the update call.

### Do the peered virtual networks also have access to Azure Cosmos account? 
Only virtual network and their subnets added to Azure Cosmos account have access. Their peered VNets cannot access the account until the subnets within peered virtual networks are added to the account.

### What is the maximum number of subnets allowed to access a single Cosmos account? 
Currently, you can have at most 256 subnets allowed for an Azure Cosmos account.

### Can I enable access from VPN and Express Route? 
For accessing Azure Cosmos account over Express route from on premises, you would need to enable Microsoft peering. Once you put IP firewall or virtual network access rules, you can add the public IP addresses used for Microsoft peering on your Azure Cosmos account IP firewall to allow on premises services access to Azure Cosmos account. 

### Do I need to update the Network Security Groups (NSG) rules? 
NSG rules are used to limit connectivity to and from a subnet with virtual network. When you add service endpoint for Azure Cosmos DB to the subnet, there is no need to open outbound connectivity in NSG for your Azure Cosmos account. 

### Are service endpoints available for all VNets?
No, Only Azure Resource Manager virtual networks can have service endpoint enabled. Classic virtual networks don't support service endpoints.

### Can I "Accept connections from within public Azure datacenters" when service endpoint access is enabled for Azure Cosmos DB?  
This is required only when you want your Azure Cosmos DB account to be accessed by other Azure first party services like Azure Data factory, Azure Cognitive Search or any service that is deployed in given Azure region.

## Next steps

* To configure a firewall for Azure Cosmos DB, see the [Firewall support](how-to-configure-firewall.md) article.