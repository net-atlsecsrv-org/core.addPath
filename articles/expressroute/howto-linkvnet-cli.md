---
title: 'Tutorial: Link a VNet to an ExpressRoute circuit - Azure CLI'
description: This tutorial shows you how to link virtual networks (VNets) to ExpressRoute circuits by using the Resource Manager deployment model and Azure CLI.
services: expressroute
author: duongau

ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau

---
# Tutorial: Connect a virtual network to an ExpressRoute circuit using CLI

This tutorial shows you how to link virtual networks (VNets) to Azure ExpressRoute circuits using Azure CLI. To link using Azure CLI, the virtual networks must be created using the Resource Manager deployment model. They can either be in the same subscription, or part of another subscription. If you want to use a different method to connect your VNet to an ExpressRoute circuit, you can select an article from the following list:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classic)](expressroute-howto-linkvnet-classic.md)
> 

In this tutorial, you learn how to:
> [!div class="checklist"]
> - Connect a virtual network in the same subscription to a circuit
> - Connect a virtual network in a different subscription to a circuit
> - Modify a virtual network connection
> - Configure ExpressRoute FastPath

## Prerequisites

* You need the latest version of the command-line interface (CLI). For more information, see [Install the Azure CLI](/cli/azure/install-azure-cli).
* Review the [prerequisites](expressroute-prerequisites.md), [routing requirements](expressroute-routing.md), and [workflows](expressroute-workflows.md) before you begin configuration.
* You must have an active ExpressRoute circuit. 
  * Follow the instructions to [create an ExpressRoute circuit](howto-circuit-cli.md) and have the circuit enabled by your connectivity provider. 
  * Ensure that you have Azure private peering configured for your circuit. See the [configure routing](howto-routing-cli.md) article for routing instructions. 
  * Ensure that Azure private peering is configured. The BGP peering between your network and Microsoft must be established so that you can enable end-to-end connectivity.
  * Ensure that you have a virtual network and a virtual network gateway created and fully provisioned. Follow the instructions to [Configure a virtual network gateway for ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Be sure to use `--gateway-type ExpressRoute`.
* You can link up to 10 virtual networks to a standard ExpressRoute circuit. All virtual networks must be in the same geopolitical region when using a standard ExpressRoute circuit. 
* A single VNet can be linked to up to four ExpressRoute circuits. Use the following process to create a new connection object for each ExpressRoute circuit you're connecting to. The ExpressRoute circuits can be in the same subscription, different subscriptions, or a mix of both.
* If you enable the ExpressRoute premium add-on, you can link virtual networks outside of the geopolitical region of the ExpressRoute circuit. The premium add-on will also allow you to connect more than 10 virtual networks to your ExpressRoute circuit depending on the bandwidth chosen. Check the [FAQ](expressroute-faqs.md) for more details on the premium add-on.

## Connect a virtual network in the same subscription to a circuit

You can connect a virtual network gateway to an ExpressRoute circuit by using the example. Make sure that the virtual network gateway is created and is ready for linking before you run the command.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## Connect a virtual network in a different subscription to a circuit

You can share an ExpressRoute circuit across multiple subscriptions. The following figure shows a simple schematic of how sharing works for ExpressRoute circuits across multiple subscriptions.

Each of the smaller clouds within the large cloud is used to represent subscriptions that belong to different departments within an organization. Each of the departments within the organization uses their own subscription for deploying their services--but they can share a single ExpressRoute circuit to connect back to your on-premises network. A single department (in this example: IT) can own the ExpressRoute circuit. Other subscriptions within the organization may use the ExpressRoute circuit.

> [!NOTE]
> Connectivity and bandwidth charges for the dedicated circuit will be applied to the ExpressRoute Circuit Owner. All virtual networks share the same bandwidth.
> 
> 

![Cross-subscription connectivity](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administration - Circuit Owners and Circuit Users

The 'Circuit Owner' is an authorized Power User of the ExpressRoute circuit resource. The Circuit Owner can create authorizations that can be redeemed by 'Circuit Users'. Circuit Users are owners of virtual network gateways that aren't within the same subscription as the ExpressRoute circuit. Circuit Users can redeem authorizations (one authorization per virtual network).

The Circuit Owner has the power to modify and revoke authorizations at any time. When an authorization is revoked, all link connections are deleted from the subscription whose access was revoked.

### Circuit Owner operations

**To create an authorization**

The circuit owner creates an authorization, which creates an authorization key to be used by a circuit user to connect their virtual network gateways to the ExpressRoute circuit. An authorization is valid for only one connection.

The following example shows how to create an authorization:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

The response contains the authorization key and status:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**To review authorizations**

The Circuit Owner can review all authorizations that are issued on a particular circuit by running the following example:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**To add authorizations**

The Circuit Owner can add authorizations by using the following example:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**To delete authorizations**

The Circuit Owner can revoke/delete authorizations to the user by running the following example:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### Circuit User operations

The Circuit User needs the peer ID and an authorization key from the Circuit Owner. The authorization key is a GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**To redeem a connection authorization**

The Circuit User can run the following example to redeem a link authorization:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**To release a connection authorization**

You can release an authorization by deleting the connection that links the ExpressRoute circuit to the virtual network.

## Modify a virtual network connection
You can update certain properties of a virtual network connection. 

**To update the connection weight**

Your virtual network can be connected to multiple ExpressRoute circuits. You may receive the same prefix from more than one ExpressRoute circuit. To choose which connection to send traffic destined for this prefix, you can change *RoutingWeight* of a connection. Traffic will be sent on the connection with the highest *RoutingWeight*.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

The range of *RoutingWeight* is 0 to 32000. The default value is 0.

## Configure ExpressRoute FastPath 
You can enable [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) if your virtual network gateway is Ultra Performance or ErGw3AZ. FastPath improves data path performance such as packets per second and connections per second between your on-premises network and your virtual network. 

**Configure FastPath on a new connection**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Updating an existing connection to enable FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```
## Clean up resources

If you no longer need the ExpressRoute connection, from the subscription where the gateway is located use the `az network vpn-connection delete` command to remove the link between the gateway and the circuit.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## Next steps

In this tutorial, you learned how to connect a virtual network to a circuit in the same subscription and a different subscription. For more information about the ExpressRoute gateway, see: 

> [!div class="nextstepaction"]
> [About ExpressRoute virtual network gateways](expressroute-about-virtual-network-gateways.md)