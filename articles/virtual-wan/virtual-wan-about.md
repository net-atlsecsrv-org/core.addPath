---
title: 'Azure Virtual WAN Overview | Microsoft Docs'
description: Learn about Virtual WAN automated scalable branch-to-branch connectivity, available regions, and partners.
services: virtual-wan
author: cherylmc

ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
---

# About Azure Virtual WAN

Azure Virtual WAN is a networking service that provides optimized and automated branch connectivity to, and through, Azure. Azure regions serve as hubs that you can choose to connect your branches to. You can leverage the Azure backbone to also connect branches and enjoy branch-to-VNet connectivity. We have a list of partners that support connectivity automation with Azure Virtual WAN VPN. For more information, see the [Virtual WAN partners and locations](virtual-wan-locations-partners.md) article.

Azure Virtual WAN brings together many Azure cloud connectivity services such as site-to-site VPN, User VPN (point-to-site), and ExpressRoute into a single operational interface. Connectivity to Azure VNets is established by using virtual network connections. It enables [global transit network architecture](virtual-wan-global-transit-network-architecture.md) based on a classic hub-and-spoke connectivity model where the cloud hosted network 'hub' enables transitive connectivity between endpoints that may be distributed across different types of 'spokes'.

![Virtual WAN diagram](./media/virtual-wan-about/virtualwan1.png)

This article provides a quick view into the network connectivity in Azure Virtual WAN. Virtual WAN offers the following advantages:

* **Integrated connectivity solutions in hub and spoke:** Automate site-to-site configuration and connectivity between on-premises sites and an Azure hub.
* **Automated spoke setup and configuration:** Connect your virtual networks and workloads to the Azure hub seamlessly.
* **Intuitive troubleshooting:** You can see the end-to-end flow within Azure, and then use this information to take required actions.

## <a name="basicstandard"></a>Basic and Standard virtual WANs

There are two types of virtual WANs: Basic and Standard. The following table shows the available configurations for each type.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

For steps to upgrade a virtual WAN, see [Upgrade a virtual WAN from Basic to Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a>Architecture

For information about Virtual WAN architecture and how to migrate to Virtual WAN, see the following articles:

* [Virtual WAN architecture](migrate-from-hub-spoke-topology.md)
* [Global transit network architecture](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Virtual WAN resources

To configure an end-to-end virtual WAN, you create the following resources:

* **virtualWAN:** The virtualWAN resource represents a virtual overlay of your Azure network and is a collection of multiple resources. It contains links to all your virtual hubs that you would like to have within the virtual WAN. Virtual WAN resources are isolated from each other and cannot contain a common hub. Virtual hubs across Virtual WAN do not communicate with each other.

* **Hub:** A virtual hub is a Microsoft-managed virtual network. The hub contains various service endpoints to enable connectivity. From your on-premises network (vpnsite), you can connect to a VPN Gateway inside the virtual hub, connect ExpressRoute circuits to a virtual hub, or even connect mobile users to a Point-to-site gateway in the virtual hub. The hub is the core of your network in a region. There can only be one hub per Azure region.

  A hub gateway is not the same as a virtual network gateway that you use for ExpressRoute and VPN Gateway. For example, when using Virtual WAN, you don't create a site-to-site connection from your on-premises site directly to your VNet. Instead, you create a site-to-site connection to the hub. The traffic always goes through the hub gateway. This means that your VNets do not need their own virtual network gateway. Virtual WAN lets your VNets take advantage of scaling easily through the virtual hub and the virtual hub gateway.

* **Hub virtual network connection:** The Hub virtual network connection resource is used to connect the hub seamlessly to your virtual network.

* **(Preview) Hub-to-Hub connection** - Hubs are all connected to each other in a virtual WAN. This implies that a branch, user, or VNet connected to a local hub can communicate with another branch or VNet using the full mesh architecture of the connected hubs. You can also connect VNets within a hub transiting through the virtual hub, as well as VNets across hub, using the hub-to-hub connected framework.

* **Hub route table:**  You can create a virtual hub route and apply the route to the virtual hub route table. You can apply multiple routes to the virtual hub route table.

**Additional Virtual WAN resources**

  * **Site:** This resource is used for site-to-site connections only. The site resource is **vpnsite**. It represents your on-premises VPN device and its settings. By working with a Virtual WAN partner, you have a built-in solution to automatically export this information to Azure.

## <a name="connectivity"></a>Types of connectivity

Virtual WAN allows the following types of connectivity: Site-to-Site VPN, User VPN (Point-to-Site), and ExpressRoute.

### <a name="s2s"></a>Site-to-site VPN connections

![Virtual WAN diagram](./media/virtual-wan-about/virtualwan.png)

When you create a virtual WAN site-to-site connection, you can work with an available partner. If you don't want to use a partner, you can configure the connection manually. For more information, see [Create a site-to-site connection using Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Virtual WAN partner workflow

When you work with a Virtual WAN partner, the workflow is:

1. The branch device (VPN/SDWAN) controller is authenticated to export site-centric information into Azure by using an [Azure Service Principal](../active-directory/develop/howto-create-service-principal-portal.md).
2. The branch device (VPN/SDWAN) controller obtains the Azure connectivity configuration and updates the local device. This automates the configuration download, editing, and updating of the on-premises VPN device.
3. Once the device has the right Azure configuration, a site-to-site connection (two active tunnels) is established to the Azure WAN. Azure supports both IKEv1 and IKEv2. BGP is optional.

#### <a name="partners"></a>Partners for site-to-site virtual WAN connections

For a list of the available partners and locations, see the [Virtual WAN partners and locations](virtual-wan-locations-partners.md) article.

### <a name="uservpn"></a>User VPN (point-to-site) connections

You can connect to your resources in Azure over an IPsec/IKE (IKEv2) or OpenVPN connection. This type of connection requires a VPN client to be configured on the client computer. For more information, see [Create a point-to-site connection](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute connections
ExpressRoute lets you connect on-premises network to Azure over a private connection. To create the connection, see [Create an ExpressRoute connection using Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Locations

For location information, see the [Virtual WAN partners and locations](virtual-wan-locations-partners.md) article.

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## Next steps

[Create a site-to-site connection using Virtual WAN](virtual-wan-site-to-site-portal.md)
