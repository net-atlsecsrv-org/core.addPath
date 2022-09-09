---
title: 'Connect your on-premises network to an Azure virtual network: Site-to-Site VPN (classic): Portal | Microsoft Docs'
description: Create an IPsec connection from your on-premises network to classic Azure virtual network over the public Internet.
services: vpn-gateway
author: cherylmc

ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc

---
# Create a Site-to-Site connection using the Azure portal (classic)

This article shows you how to use the Azure portal to create a Site-to-Site VPN gateway connection from your on-premises network to the VNet. The steps in this article apply to the classic deployment model and do not apply to the current deployment model, Resource Manager. You can also create this configuration using a different deployment tool or deployment model by selecting a different option from the following list:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure portal (classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

A Site-to-Site VPN gateway connection is used to connect your on-premises network to an Azure virtual network over an IPsec/IKE (IKEv1 or IKEv2) VPN tunnel. This type of connection requires a VPN device located on-premises that has an externally facing public IP address assigned to it. For more information about VPN gateways, see [About VPN gateway](vpn-gateway-about-vpngateways.md).

![Site-to-Site VPN Gateway cross-premises connection diagram](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before"></a>Before you begin

Verify that you have met the following criteria before beginning configuration:

* Verify that you want to work in the classic deployment model. If you want to work in the Resource Manager deployment model, see [Create a Site-to-Site connection (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). We recommend that you use the Resource Manager deployment model, as the classic model is legacy.
* Make sure you have a compatible VPN device and someone who is able to configure it. For more information about compatible VPN devices and device configuration, see [About VPN Devices](vpn-gateway-about-vpn-devices.md).
* Verify that you have an externally facing public IPv4 address for your VPN device.
* If you are unfamiliar with the IP address ranges located in your on-premises network configuration, you need to coordinate with someone who can provide those details for you. When you create this configuration, you must specify the IP address range prefixes that Azure will route to your on-premises location. None of the subnets of your on-premises network can over lap with the virtual network subnets that you want to connect to.
* PowerShell is required in order to specify the shared key and create the VPN gateway connection. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="values"></a>Sample configuration values for this exercise

The examples in this article use the following values. You can use these values to create a test environment, or refer to them to better understand the examples in this article. Typically, when working with IP address values for Address space, you want to coordinate with your network administrator in order to avoid overlapping address spaces, which can affect routing. In this case, replace the IP address values with your own if you want to create a working connection.

* **Resource Group:** TestRG1
* **VNet Name:** TestVNet1
* **Address space:** 10.11.0.0/16
* **Subnet name:** FrontEnd
* **Subnet address range:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (US) East US
* **Local site name:** Site2
* **Client address space:** The address space that is located on your on-premises site.

## <a name="CreatVNet"></a>Create a virtual network

When you create a virtual network to use for a S2S connection, you need to make sure that the address spaces that you specify do not overlap with any of the client address spaces for the local sites that you want to connect to. If you have overlapping subnets, your connection won't work properly.

* If you already have a VNet, verify that the settings are compatible with your VPN gateway design. Pay particular attention to any subnets that may overlap with other networks.

* If you don't already have a virtual network, create one. Screenshots are provided as examples. Be sure to replace the values with your own.

### To create a virtual network

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="localsite"></a>Configure the site and gateway

### To configure the site

The local site typically refers to your on-premises location. It contains the IP address of the VPN device to which you will create a connection, and the IP address ranges that will be routed through the VPN gateway to the VPN device.

1. On the page for your VNet, under **Settings**, select **Site-to-site connections**.
1. On the Site-to-site connections page, select **+ Add**.
1. On the **Configure a VPN connection and gateway** page, for **Connection type**, leave **Site-to-site** selected. For this exercise, you will need to use a combination of the [example values](#values) and your own values.

   * **VPN gateway IP address:** This is the public IP address of the VPN device for your on-premises network. The VPN device requires an IPv4 public IP address. Specify a valid public IP address for the VPN device to which you want to connect. It must be reachable by Azure. If you don't know the IP address of your VPN device, you can always put in a placeholder value (as long as it is in the format of a valid public IP address) and then change it later.

   * **Client Address space:** List the IP address ranges that you want routed to the local on-premises network through this gateway. You can add multiple address space ranges. Make sure that the ranges you specify here do not overlap with ranges of other networks your virtual network connects to, or with the address ranges of the virtual network itself.
1. At the bottom of the page, DO NOT select Review + create. Instead, select **Next: Gateway>**.

### <a name="sku"></a>To configure the virtual network gateway

1. On the **Gateway** page, select the following values:

   * **Size:** This is the gateway SKU that you use to create your virtual network gateway. Classic VPN gateways use the old (legacy) gateway SKUs. For more information about the legacy gateway SKUs, see [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md). You can select **Standard** for this exercise.

   * **Routing type:** Select the routing type for your gateway. This is also known as the VPN type. It's important to select the correct type because you cannot convert the gateway from one type to another. Your VPN device must be compatible with the routing type you select. For more information about Routing Type, see [About VPN Gateway Settings](vpn-gateway-about-vpn-gateway-settings.md#vpntype). You may see articles referring to 'RouteBased' and 'PolicyBased' VPN types. 'Dynamic' corresponds to 'RouteBased', and 'Static' corresponds to' PolicyBased'. Typically, you want Dynamic routing.

   * **Gateway subnet:** The size of the gateway subnet that you specify depends on the VPN gateway configuration that you want to create. While it is possible to create a gateway subnet as small as /29, we recommend that you use /27 or /28. This creates a larger subnet that includes more addresses. Using a larger gateway subnet allows for enough IP addresses to accommodate possible future configurations.

1. Select **Review + create** at the bottom of the page to validate your settings. Select **Create** to deploy. It can take up to 45 minutes to create a virtual network gateway, depending on the gateway SKU that you selected.

## <a name="vpndevice"></a>Configure your VPN device

Site-to-Site connections to an on-premises network require a VPN device. In this step, you configure your VPN device. When configuring your VPN device, you need the following values:

* A shared key. This is the same shared key that you specify when creating your Site-to-Site VPN connection. In our examples, we use a basic shared key. We recommend that you generate a more complex key to use.
* The Public IP address of your virtual network gateway. You can view the public IP address by using the Azure portal, PowerShell, or CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="getvalues"></a>Retrieve values

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="CreateConnection"></a>Create the connection

> [!NOTE]
> For the classic deployment model, this step is not available in the Azure portal or via Azure Cloud Shell. You must use the Service Management (SM) version of the Azure PowerShell cmdlets locally from your desktop.
>

In this step, using the values from the previous steps, you set the shared key and create the connection. The key you set is must be the same key that was used in your VPN device configuration.

1. Set the shared key and create the connection.

   * Change the -VNetName value and the -LocalNetworkSiteName value. When specifying a name that contains spaces, use single quotation marks around the value.
   * The '-SharedKey' is a value that you generate, and then specify. In the example, we used 'abc123', but you can (and should) generate something more complex. The important thing is that the value you specify here must be the same value that you specified when configuring your VPN device.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. When the connection is created, the result is: **Status: Successful**.

## <a name="verify"></a>Verify your connection

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

If you are having trouble connecting, see the **Troubleshoot** section of the table of contents in the left pane.

## <a name="reset"></a>How to reset a VPN gateway

Resetting an Azure VPN gateway is helpful if you lose cross-premises VPN connectivity on one or more Site-to-Site VPN tunnels. In this situation, your on-premises VPN devices are all working correctly, but are not able to establish IPsec tunnels with the Azure VPN gateways. For steps, see [Reset a VPN gateway](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="changesku"></a>How to change a gateway SKU

For steps to change a gateway SKU, see [Resize a gateway SKU](vpn-gateway-about-SKUS-legacy.md#classicresize).

## Next steps

* Once your connection is complete, you can add virtual machines to your virtual networks. For more information, see [Virtual Machines](https://docs.microsoft.com/azure/).
* For information about Forced Tunneling, see [About Forced Tunneling](vpn-gateway-about-forced-tunneling.md).
