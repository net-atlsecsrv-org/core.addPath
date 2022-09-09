---
title: Networking overview - Azure Database for PostgreSQL - Flexible Server
description: Learn about connectivity and networking options in the Flexible Server deployment option for Azure Database for PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/22/2021
---

# Networking overview - Azure Database for PostgreSQL - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview

This article describes connectivity and networking concepts for Azure Database for PostgreSQL - Flexible Server. 

## Choosing a networking option
You have two networking options for your Azure Database for PostgreSQL - Flexible Server. The options are **private access (VNet integration)** and **public access (allowed IP addresses)**. At server creation, you must pick one option. 

> [!NOTE]
> Your networking option cannot be changed after the server is created. 

* **Private access (VNet integration)** – You can deploy your flexible server into your [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Azure virtual networks provide private and secure network communication. Resources in a virtual network can communicate through private IP addresses.

   Choose the VNet Integration option if you want the following capabilities:
   * Connect from Azure resources in the same virtual network to your flexible server using private IP addresses
   * Use VPN or ExpressRoute to connect from non-Azure resources to your flexible server
   * The flexible server has no public endpoint

* **Public access (allowed IP addresses)** – Your flexible server is accessed through a public endpoint. The public endpoint is a publicly resolvable DNS address. The phrase “allowed IP addresses” refers to a range of IPs you choose to give permission to access your server. These permissions are called **firewall rules**. 

   Choose the public access method if you want the following capabilities:
   * Connect from Azure resources that do not support virtual networks
   * Connect from resources outside of an Azure that are not connected by VPN or ExpressRoute 
   * The flexible server has a public endpoint

The following characteristics apply whether you choose to use the private access or the public access option:
* Connections from allowed IP addresses need to authenticate to the PostgreSQL server with valid credentials
* [Connection encryption](#tls-and-ssl) is available for your network traffic
* The server has a fully qualified domain name (fqdn). For the hostname property in connection strings, we recommend using the fqdn instead of an IP address.
* Both options control access at the server-level, not at the database- or table-level. You would use PostgreSQL’s roles properties to control database, table, and other object access.


## Private access (VNet integration)
Private access with virtual network (vnet) integration provides private and secure communication for your PostgreSQL flexible server.

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="Flexible server Postgres VNET":::

In the above diagram,
1. Flexible servers are injected into a delegated subnet - 10.0.1.0/24 of VNET **VNet-1**.
2. Applications that are deployed on different subnets within the same vnet can access the Flexible servers directly.
3. Applications that are deployed on a different VNET **VNet-2** do not have direct access to flexible servers. You have to perform [private DNS zone VNET peering](#private-dns-zone-and-vnet-peering) before they can access the flexible server.
   
### Virtual network concepts
Here are some concepts to be familiar with when using virtual networks with PostgreSQL flexible servers.

* **Virtual network** - 
   An Azure Virtual Network (VNet) contains a private IP address space that is configured for your use. Visit the [Azure Virtual Network overview](../../virtual-network/virtual-networks-overview.md) to learn more about Azure virtual networking.

    Your virtual network must be in the same Azure region as your flexible server.

* **Delegated subnet** - 
   A virtual network contains subnets (sub-networks). Subnets enable you to segment your virtual network into smaller address spaces. Azure resources are deployed into specific subnets within a virtual network. 

   Your PostgreSQL flexible server must be in a subnet that is **delegated** for PostgreSQL flexible server use only. This delegation means that only Azure Database for PostgreSQL Flexible Servers can use that subnet. No other Azure resource types can be in the delegated subnet. You delegate a subnet by assigning its delegation property as Microsoft.DBforPostgreSQL/flexibleServers.

* **Network security groups (NSG)** - 
   Security rules in network security groups enable you to filter the type of network traffic that can flow in and out of virtual network subnets and network interfaces. See [network security group overview](../../virtual-network/network-security-groups-overview.md) documentation for more information.

* **Private DNS zone integration** - 
   Azure private DNS zone integration allows you to resolve the private DNS within the current VNET or any in-region peered VNET where the private DNS Zone is linked. See [private DNS zone documentation](https://docs.microsoft.com/azure/dns/private-dns-overview) for more details.

Learn how to create a flexible server with private access (VNet integration) in [the Azure portal](how-to-manage-virtual-network-portal.md) or [the Azure CLI](how-to-manage-virtual-network-cli.md).

### Integration with custom DNS server

If you are using the custom DNS server then you must use a DNS forwarder to resolve the FQDN of Azure Database for PostgreSQL - Flexible Server. The forwarder IP address should be [168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16) and the custom DNS server should be inside the VNet. Refer to [name resolution that uses your own DNS server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) to learn more.

### Private DNS zone and VNET peering

Private DNS zone settings and VNET peering are independent of each other.

* By default, a new private DNS zone is auto-provisioned per server using the server name provided. However, if you want to setup your own private DNS zone to use with the flexible server, please see the [private DNS overview](https://docs.microsoft.com/azure/dns/private-dns-overview) documentation.
* If you want to connect to the flexible server from a client that is provisioned in another VNET, you have to link the private DNS zone with the VNET. See [how to link the virtual network](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) documentation.

> [!NOTE]
> Private DNS zone names that end with `private.postgres.database.azure.com` can only be linked.

### Unsupported virtual network scenarios
* Public endpoint (or public IP or DNS) - A flexible server deployed to a virtual network cannot have a public endpoint
* After the flexible server is deployed to a virtual network and subnet, you cannot move it to another virtual network or subnet. You cannot move the virtual network into another resource group or subscription.
* Subnet size (address spaces) cannot be increased once resources exist in the subnet
* Peering VNets across regions is not supported


## Public access (allowed IP addresses)
Characteristics of the public access method include:
* Only the IP addresses you allow have permission to access your PostgreSQL flexible server. By default no IP addresses are allowed. You can add IP addresses during server creation or afterwards.
* Your PostgreSQL server has a publicly resolvable DNS name
* Your flexible server is not in one of your Azure virtual networks
* Network traffic to and from your server does not go over a private network. The traffic uses the general internet pathways.

### Firewall rules
Granting permission to an IP address is called a firewall rule. If a connection attempt comes from an IP address you have not allowed, the originating client will see an error.

Learn how to create a flexible server with public access (allowed IP addresses) in [the Azure portal](how-to-manage-firewall-portal.md) or [the Azure CLI](how-to-manage-firewall-cli.md).


### Allowing all Azure IP addresses
If a fixed outgoing IP address isn't available for your Azure service, you can consider enabling connections from all Azure datacenter IP addresses.

> [!IMPORTANT]
> The **Allow public access from Azure services and resources within Azure** option configures the firewall to allow all connections from Azure, including connections from the subscriptions of other customers. When selecting this option, make sure your login and user permissions limit access to only authorized users.

### Troubleshooting public access issues
Consider the following points when access to the Microsoft Azure Database for PostgreSQL Server service does not behave as you expect:

* **Changes to the allowlist have not taken effect yet:** There may be as much as a five-minute delay for changes to the Azure Database for PostgreSQL Server firewall configuration to take effect.

* **Authentication failed:** If a user does not have permissions on the Azure Database for PostgreSQL server or the password used is incorrect, the connection to the Azure Database for PostgreSQL server is denied. Creating a firewall setting only provides clients with an opportunity to attempt connecting to your server. Each client must still provide the necessary security credentials.

* **Dynamic client IP address:** If you have an Internet connection with dynamic IP addressing and you are having trouble getting through the firewall, you could try one of the following solutions:

   * Ask your Internet Service Provider (ISP) for the IP address range assigned to your client computers that access the Azure Database for PostgreSQL Server, and then add the IP address range as a firewall rule.
   * Get static IP addressing instead for your client computers, and then add the static IP address as a firewall rule.

* **Firewall rule is not available for IPv6 format:** The firewall rules must be in IPv4 format. If you specify firewall rules in IPv6 format, it will show the validation error.

## Hostname
Regardless of the networking option you choose, we recommend you always use a fully qualified domain name (FQDN) as hostname when connecting to your flexible server. The server’s IP address is not guaranteed to remain static. Using the FQDN will help you avoid making changes to your connection string. 

Example
* Recommended `hostname = servername.postgres.database.azure.com`
* Where possible, avoid using `hostname = 10.0.0.4` (a private address) or `hostname = 40.2.45.67` (a public address)


## TLS and SSL
Azure Database for PostgreSQL - Flexible Server supports connecting your client applications to the PostgreSQL service using Transport Layer Security (TLS). TLS is an industry standard protocol that ensures encrypted network connections between your database server and client applications. TLS is an updated protocol of SSL (Secure Sockets Layer).

Azure Database for PostgreSQL - Flexible Server only supports encrypted connections using Transport Layer Security. All incoming connections with TLS 1.0 and TLS 1.1 will be denied. 

## Next steps
* Learn how to create a flexible server with **private access (VNet integration)** in [the Azure portal](how-to-manage-virtual-network-portal.md) or [the Azure CLI](how-to-manage-virtual-network-cli.md).
* Learn how to create a flexible server with **public access (allowed IP addresses)** in [the Azure portal](how-to-manage-firewall-portal.md) or [the Azure CLI](how-to-manage-firewall-cli.md).
