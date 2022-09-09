---
title: 'Tutorial - Connect on-premises network to virtual network: Azure portal'
description: Create a site-to-site VPN Gateway IPsec connection from your on-premises network to an Azure virtual network over the public internet using the portal.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020

---

# Tutorial: Create a Site-to-Site connection in the Azure portal

Azure VPN gateways provide cross-premises connectivity between customer premises and Azure. This tutorial shows you how to use the Azure portal to create a Site-to-Site VPN gateway connection from your on-premises network to the VNet. You can also create this configuration using [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) or [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Site-to-Site VPN Gateway cross-premises connection diagram":::

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a virtual network
> * Create a VPN gateway
> * Create a local network gateway
> * Create a VPN connection
> * Verify the connection
> * Connect to a virtual machine

## Prerequisites

* An Azure account with an active subscription. If you don't have one, [create one for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Make sure you have a compatible VPN device and someone who is able to configure it. For more information about compatible VPN devices and device configuration, see [About VPN Devices](vpn-gateway-about-vpn-devices.md).
* Verify that you have an externally facing public IPv4 address for your VPN device.
* If you are unfamiliar with the IP address ranges located in your on-premises network configuration, you need to coordinate with someone who can provide those details for you. When you create this configuration, you must specify the IP address range prefixes that Azure will route to your on-premises location. None of the subnets of your on-premises network can over lap with the virtual network subnets that you want to connect to.

## <a name="CreatVNet"></a>Create a virtual network

Create a virtual network (VNet) using the following values:

* **Resource group:** TestRG1
* **Name:** VNet1
* **Region:** (US) East US
* **IPv4 address space:** 10.1.0.0/16
* **Subnet name:** FrontEnd
* **Subnet address space:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>Create a VPN gateway

In this step, you create the virtual network gateway for your VNet. Creating a gateway can often take 45 minutes or more, depending on the selected gateway SKU.

### About the gateway subnet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### Create the gateway

Create a VPN gateway using the following values:

* **Name:** VNet1GW
* **Region:** East US
* **Gateway type:** VPN
* **VPN type:** Route-based
* **SKU:** VpnGw1
* **Generation:** Generation1
* **Virtual network:** VNet1
* **Gateway subnet address range:** 10.1.255.0/27
* **Public IP address:** Create new
* **Public IP address name:** VNet1GWpip
* **Enable active-active mode:** Disabled
* **Configure BGP:** Disabled

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view"></a>View the public IP address

You can view the gateway public IP address on the **Overview** page for your gateway.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Overview page":::

To see additional information about the public IP address object, click the name/IP address link next to **Public IP address**.

## <a name="LocalNetworkGateway"></a>Create a local network gateway

The local network gateway is a specific object that represents your on-premises location (the site) for routing purposes. You give the site a name by which Azure can refer to it, then specify the IP address of the on-premises VPN device to which you will create a connection. You also specify the IP address prefixes that will be routed through the VPN gateway to the VPN device. The address prefixes you specify are the prefixes located on your on-premises network. If your on-premises network changes or you need to change the public IP address for the VPN device, you can easily update the values later.

Create a local network gateway using the following values:

* **Name:** Site1
* **Resource Group:** TestRG1
* **Location:** East US

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="VPNDevice"></a>Configure your VPN device

Site-to-Site connections to an on-premises network require a VPN device. In this step, you configure your VPN device. When configuring your VPN device, you need the following values:

* A shared key. This is the same shared key that you specify when creating your Site-to-Site VPN connection. In our examples, we use a basic shared key. We recommend that you generate a more complex key to use.
* The Public IP address of your virtual network gateway. You can view the public IP address by using the Azure portal, PowerShell, or CLI. To find the Public IP address of your VPN gateway using the Azure portal, navigate to **Virtual network gateways**, then select the name of your gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="CreateConnection"></a>Create a VPN connection

Create the Site-to-Site VPN connection between your virtual network gateway and your on-premises VPN device.

Create a connection using the following values:

* **Local network gateway name:** Site1
* **Connection name:** VNet1toSite1
* **Shared key:** For this example, we use abc123. But, you can use whatever is compatible with your VPN hardware. The important thing is that the values match on both sides of the connection.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="VerifyConnection"></a>Verify the VPN connection

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connectVM"></a>Connect to a virtual machine

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## Optional steps

### <a name="addconnect"></a>Add additional connections to the gateway

You can add additional connections, provided that none of the address spaces overlap between connections.

1. To add an additional connection, navigate to the VPN gateway, then select **Connections** to open the Connections page.
1. Select **+Add** to add your connection. Adjust the connection type to reflect either VNet-to-VNet (if connecting to another VNet gateway), or Site-to-site.
1. If you are connecting using Site-to-site and you have not already created a local network gateway for the site you want to connect to, you can create a new one.
1. Specify the shared key that you want to use, then select **OK** to create the connection.

### <a name="resize"></a>Resize a gateway SKU

There are specific rules regarding resizing vs. changing a gateway SKU. In this section, we will resize the SKU. For more information, see [Gateway settings - resizing and changing SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset"></a>Reset a gateway

Resetting an Azure VPN gateway is helpful if you lose cross-premises VPN connectivity on one or more Site-to-Site VPN tunnels. In this situation, your on-premises VPN devices are all working correctly, but are not able to establish IPsec tunnels with the Azure VPN gateways.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="connectVM"></a>Additional configuration considerations

S2S configurations can be customized in a variety of ways. For more information, see the following articles:

* For information about BGP, see the [BGP Overview](vpn-gateway-bgp-overview.md) and [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md).
* For information about forced tunneling, see [About forced tunneling](vpn-gateway-forced-tunneling-rm.md).
* For information about Highly Available Active-Active connections, see [Highly Available cross-premises and VNet-to-VNet connectivity](vpn-gateway-highlyavailable.md).
* For information about how to limit network traffic to resources in a virtual network, see [Network Security](../virtual-network/network-security-groups-overview.md).
* For information about how Azure routes traffic between Azure, on-premises, and Internet resources, see [Virtual network traffic routing](../virtual-network/virtual-networks-udr-overview.md).

## Clean up resources

If you're not going to continue to use this application or go to the next tutorial, delete
these resources using the following steps:

1. Enter the name of your resource group in the **Search** box at the top of the portal and select it from the search results.

1. Select **Delete resource group**.

1. Enter your resource group for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

Once you have configured a S2S connection, you can add a P2S connection to the same gateway.

> [!div class="nextstepaction"]
> [Point-to-Site VPN connections](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
