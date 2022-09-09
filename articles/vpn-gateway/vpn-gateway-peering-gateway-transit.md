---
title: 'Configure VPN gateway transit for virtual network peering'
description: Configure gateway transit for virtual network peering, to seamlessly connect two Azure virtual networks into one for connectivity purposes.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc

ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc

---
# Configure VPN gateway transit for virtual network peering

This article helps you configure gateway transit for virtual network peering. [Virtual network peering](../virtual-network/virtual-network-peering-overview.md) seamlessly connects two Azure virtual networks, merging the two virtual networks into one for connectivity purposes. [Gateway transit](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is a peering property that lets one virtual network use the VPN gateway in the peered virtual network for cross-premises or VNet-to-VNet connectivity. The following diagram shows how gateway transit works with virtual network peering.

![Gateway transit diagram](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

In the diagram, gateway transit allows the peered virtual networks to use the Azure VPN gateway in Hub-RM. Connectivity available on the VPN gateway, including S2S, P2S, and VNet-to-VNet connections, applies to all three virtual networks. The transit option is available for peering between the same, or different deployment models. If you are configuring transit between different deployment models, the hub virtual network and virtual network gateway must be in the Resource Manager deployment model, not the classic deployment model.
>

In hub-and-spoke network architecture, gateway transit allows spoke virtual networks to share the VPN gateway in the hub, instead of deploying VPN gateways in every spoke virtual network. Routes to the gateway-connected virtual networks or on-premises networks will propagate to the routing tables for the peered virtual networks using gateway transit. You can disable the automatic route propagation from the VPN gateway. Create a routing table with the "**Disable BGP route propagation**" option, and associate the routing table to the subnets to prevent the route distribution to those subnets. For more information, see [Virtual network routing table](../virtual-network/manage-route-table.md).

There are two scenarios in this article:

* **Same deployment model**: Both virtual networks are created in the Resource Manager deployment model.
* **Different deployment models**: The spoke virtual network is created in the classic deployment model, and the hub virtual network and gateway are in the Resource Manager deployment model.

>[!NOTE]
> If you make a change to the topology of your network and have Windows VPN clients, the VPN client package for Windows clients must be downloaded and installed again in order for the changes to be applied to the client.
>

## Prerequisites

Before you begin, verify that you have the following virtual networks and permissions:

### <a name="vnet"></a>Virtual networks

|VNet|Deployment model| Virtual network gateway|
|---|---|---|---|
| Hub-RM| [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| [Yes](tutorial-create-gateway-portal.md)|
| Spoke-RM | [Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md)| No |
| Spoke-Classic | [Classic](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | No |

### <a name="permissions"></a>Permissions

The accounts you use to create a virtual network peering must have the necessary roles or permissions. In the example below, if you were peering the two virtual networks named **Hub-RM** and **Spoke-Classic**, your account must have the following roles or permissions for each virtual network:

|VNet|Deployment model|Role|Permissions|
|---|---|---|---|
|Hub-RM|Resource Manager|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classic|[Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|Spoke-Classic|Resource Manager|[Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classic|[Classic Network Contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Learn more about [built-in roles](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) and assigning specific permissions to [custom roles](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Resource Manager only).

## <a name="same"></a>Same deployment model

In this scenario, the virtual networks are both in the Resource Manager deployment model. Use the following steps to create or update the virtual network peerings to enable gateway transit.

### To add a peering and enable transit

1. In the [Azure portal](https://portal.azure.com), create or update the virtual network peering from the Hub-RM. Navigate to the **Hub-RM** virtual network. Select **Peerings**, then **+ Add** to open **Add peering**.
1. On the **Add peering** page, configure the values for **This virtual network**.

   * Peering link name: Name the link. Example: **HubRMToSpokeRM**
   * Traffic to remote virtual network: **Allow**
   * Traffic forwarded from remote virtual network: **Allow**
   * Virtual network gateway: **Use this virtual network's gateway**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Screenshot shows add peering.":::

1. On the same page, continue on to configure the values for the **Remote virtual network**.

   * Peering link name: Name the link. Example: **SpokeRMtoHubRM**
   * Deployment model: **Resource Manager**
   * Virtual Network: **Spoke-RM**
   * Traffic to remote virtual network: **Allow**
   * Traffic forwarded from remote virtual network: **Allow**
   * Virtual network gateway: **Use the remote virtual network's gateway**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Screenshot shows values for remote virtual network.":::

1. Select **Add** to create the peering.
1. Verify the peering status as **Connected** on both virtual networks.

### To modify an existing peering for transit

If the peering was already created, you can modify the peering for transit.

1. Navigate to the virtual network. Select **Peerings** and select the peering that you want to modify.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Screenshot shows select peerings.":::

1. Update the VNet peering.

   * Traffic to remote virtual network: **Allow**
   * Traffic forwarded to virtual network; **Allow**
   * Virtual network gateway: **Use remote virtual network's gateway**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Screenshot shows modify peering gateway.":::

1. **Save** the peering settings.

### <a name="ps-same"></a>PowerShell sample

You can also use PowerShell to create or update the peering with the example above. Replace the variables with the names of your virtual networks and resource groups.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="different"></a>Different deployment models

In this configuration, the spoke VNet **Spoke-Classic** is in the classic deployment model and the hub VNet **Hub-RM** is in the Resource Manager deployment model. When configuring transit between deployment models, the virtual network gateway must be configured for the Resource Manager VNet, not the classic VNet.

For this configuration, you only need to configure the **Hub-RM** virtual network. You don't need to configure anything on the **Spoke-Classic** VNet.

1. In the Azure portal, navigate to the **Hub-RM** virtual network, select **Peerings**, then select **+ Add**.
1. On the **Add peering** page, configure the following values:

   * Peering link name: Name the link. Example: **HubRMToClassic**
   * Traffic to remote virtual network: **Allow**
   * Traffic forwarded from remote virtual network: **Allow**
   * Virtual network gateway: **Use this virtual network's gateway**
   * Remote virtual network: **Classic**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Add peering page for Spoke-Classic":::

1. Verify the subscription is correct, then select the virtual network from the dropdown.
1. Select **Add** to add the peering.
1. Verify the peering status as **Connected** on the Hub-RM virtual network. 

For this configuration, you do not need to configure anything on the **Spoke-Classic** virtual network. Once the status shows **Connected**, the spoke virtual network can use the connectivity through the VPN gateway in the hub virtual network.

### <a name="ps-different"></a>PowerShell sample

You can also use PowerShell to create or update the peering with the example above. Replace the variables and subscription ID with the values of your virtual network and resource groups, and subscription. You only need to create virtual network peering on the hub virtual network.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## Next steps

* Learn more about [virtual network peering constraints and behaviors](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) and [virtual network peering settings](../virtual-network/virtual-network-manage-peering.md#create-a-peering) before creating a virtual network peering for production use.
* Learn how to [create a hub and spoke network topology](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) with virtual network peering and gateway transit.
* [Create virtual network peering with the same deployment model](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Create virtual network peering with different deployment models](../virtual-network/create-peering-different-deployment-models.md).