---
title: Configure a virtual network - Premium-tier Azure Cache for Redis instance
description: Learn how to create and manage virtual network support for your Premium-tier Azure Cache for Redis instances.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
---
# Configure virtual network support for a Premium-tier Azure Cache for Redis instance

Azure Cache for Redis has different cache offerings, which provide flexibility in the choice of cache size and features. Premium-tier features include clustering, persistence, and virtual network support. A virtual network is a private network in the cloud. When an Azure Cache for Redis instance is configured with a virtual network, it isn't publicly addressable and can only be accessed from virtual machines and applications within the virtual network. This article describes how to configure virtual network support for a Premium-tier Azure Cache for Redis instance.

> [!NOTE]
> Azure Cache for Redis supports both classic deployment model and Azure Resource Manager virtual networks.
> 

## Why Virtual Network?

[Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) deployment provides enhanced security and isolation for your Azure Cache for Redis instance, along with subnets, access control policies, and other features to further restrict access.

## Virtual network support

Virtual network support is configured on the **New Azure Cache for Redis** pane during cache creation.

1. To create a Premium-tier cache, sign in to the [Azure portal](https://portal.azure.com) and select **Create a resource**. In addition to creating caches in the Azure portal, you can also create them by using Resource Manager templates, PowerShell, or the Azure CLI. For more information about how to create an Azure Cache for Redis instance, see [Create a cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Screenshot that shows Create a resource.":::
   
1. On the **New** page, select **Databases**. Then select **Azure Cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Screenshot that shows selecting Azure Cache for Redis.":::

1. On the **New Redis Cache** page, configure the settings for your new Premium-tier cache.
   
   | Setting      | Suggested value  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS name** | Enter a globally unique name. | The cache name must be a string between 1 and 63 characters that contain only numbers, letters, or hyphens. The name must start and end with a number or letter, and it can't contain consecutive hyphens. Your cache instance's *host name* will be *\<DNS name>.redis.cache.windows.net*. |
   | **Subscription** | Select your subscription from the drop-down list. | The subscription under which to create this new Azure Cache for Redis instance. |
   | **Resource group** | Select a resource group from the drop-down list, or select **Create new** and enter a new resource group name. | The name for the resource group in which to create your cache and other resources. By putting all your app resources in one resource group, you can easily manage or delete them together. |
   | **Location** | Select a location from the drop-down list. | Select a [region](https://azure.microsoft.com/regions/) near other services that will use your cache. |
   | **Cache type** |Select a Premium-tier cache from the drop-down list to configure Premium-tier features. For more information, see [Azure Cache for Redis pricing](https://azure.microsoft.com/pricing/details/cache/). |  The pricing tier determines the size, performance, and features that are available for the cache. For more information, see [Azure Cache for Redis overview](cache-overview.md). |

1. Select the **Networking** tab, or select the **Networking** button at the bottom of the page.

1. On the **Networking** tab, select **Virtual Networks** as your connectivity method. To use a new virtual network, create it first by following the steps in [Create a virtual network using the Azure portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) or [Create a virtual network (classic) by using the Azure portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Then return to the **New Azure Cache for Redis** pane to create and configure your Premium-tier cache.

   > [!IMPORTANT]
   > When you deploy Azure Cache for Redis to a Resource Manager virtual network, the cache must be in a dedicated subnet that contains no other resources except for Azure Cache for Redis instances. If you attempt to deploy an Azure Cache for Redis instance to a Resource Manager virtual network subnet that contains other resources, the deployment fails.
   > 
   > 

   | Setting      | Suggested value  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtual network** | Select your virtual network from the drop-down list. | Select a virtual network that's in the same subscription and location as your cache. |
   | **Subnet** | Select your subnet from the drop-down list. | The subnet's address range should be in CIDR notation (for example, 192.168.1.0/24). It must be contained by the address space of the virtual network. |
   | **Static IP address** | (Optional) Enter a static IP address. | If you don't specify a static IP address, an IP address is chosen automatically. |

   > [!IMPORTANT]
   > Azure reserves some IP addresses within each subnet, and these addresses can't be used. The first and last IP addresses of the subnets are reserved for protocol conformance, along with three more addresses used for Azure services. For more information, see [Are there any restrictions on using IP addresses within these subnets?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > In addition to the IP addresses used by the Azure virtual network infrastructure, each Azure Cache for Redis instance in the subnet uses two IP addresses per shard and one additional IP address for the load balancer. A nonclustered cache is considered to have one shard.
   > 

1. Select the **Next: Advanced** tab, or select the **Next: Advanced** button at the bottom of the page.

1. On the **Advanced** tab for a Premium-tier cache instance, configure the settings for non-TLS port, clustering, and data persistence.

1. Select the **Next: Tags** tab, or select the **Next: Tags** button at the bottom of the page.

1. Optionally, on the **Tags** tab, enter the name and value if you want to categorize the resource.

1. Select **Review + create**. You're taken to the **Review + create** tab where Azure validates your configuration.

1. After the green **Validation passed** message appears, select **Create**.

It takes a while for the cache to create. You can monitor progress on the Azure Cache for Redis **Overview** page. When **Status** shows as **Running**, the cache is ready to use. After the cache is created, you can view the configuration for the virtual network by selecting **Virtual Network** from the **Resource** menu.

![Virtual network][redis-cache-vnet-info]

To connect to your Azure Cache for Redis instance when you use a virtual network, specify the host name of your cache in the connection string, as shown in the following example:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## Azure Cache for Redis virtual network FAQ

The following list contains answers to commonly asked questions about Azure Cache for Redis scaling.

* What are some common misconfiguration issues with Azure Cache for Redis and virtual networks?
* [How can I verify that my cache is working in a virtual network?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* When I try to connect to my Azure Cache for Redis instance in a virtual network, why do I get an error stating the remote certificate is invalid?
* [Can I use virtual networks with a standard or basic cache?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Why does creating an Azure Cache for Redis instance fail in some subnets but not others?
* [What are the subnet address space requirements?](#what-are-the-subnet-address-space-requirements)
* [Do all cache features work when a cache is hosted in a virtual network?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### What are some common misconfiguration issues with Azure Cache for Redis and virtual networks?

When Azure Cache for Redis is hosted in a virtual network, the ports in the following tables are used.

>[!IMPORTANT]
>If the ports in the following tables are blocked, the cache might not function correctly. Having one or more of these ports blocked is the most common misconfiguration issue when you use Azure Cache for Redis in a virtual network.
> 

- [Outbound port requirements](#outbound-port-requirements)
- [Inbound port requirements](#inbound-port-requirements)

#### Outbound port requirements

There are nine outbound port requirements. Outbound requests in these ranges are either outbound to other services necessary for the cache to function or internal to the Redis subnet for internode communication. For geo-replication, additional outbound requirements exist for communication between subnets of the primary and replica cache.

| Ports | Direction | Transport protocol | Purpose | Local IP | Remote IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Outbound |TCP |Redis dependencies on Azure Storage/PKI (internet) | (Redis subnet) |* <sup>4</sup> |
| 443 | Outbound | TCP | Redis dependency on Azure Key Vault and Azure Monitor | (Redis subnet) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Outbound |TCP/UDP |Redis dependencies on DNS (internet/virtual network) | (Redis subnet) | 168.63.129.16 and 169.254.169.254 <sup>2</sup> and any custom DNS server for the subnet <sup>3</sup> |
| 8443 |Outbound |TCP |Internal communications for Redis | (Redis subnet) | (Redis subnet) |
| 10221-10231 |Outbound |TCP |Internal communications for Redis | (Redis subnet) | (Redis subnet) |
| 20226 |Outbound |TCP |Internal communications for Redis | (Redis subnet) |(Redis subnet) |
| 13000-13999 |Outbound |TCP |Internal communications for Redis | (Redis subnet) |(Redis subnet) |
| 15000-15999 |Outbound |TCP |Internal communications for Redis and geo-replication | (Redis subnet) |(Redis subnet) (Geo-replica peer subnet) |
| 6379-6380 |Outbound |TCP |Internal communications for Redis | (Redis subnet) |(Redis subnet) |

<sup>1</sup> You can use the service tags AzureKeyVault and AzureMonitor with Resource Manager network security groups (NSGs).

<sup>2</sup> These IP addresses owned by Microsoft are used to address the host VM that serves Azure DNS.

<sup>3</sup> This information isn't needed for subnets with no custom DNS server or newer Redis caches that ignore custom DNS.

<sup>4</sup> For more information, see [Additional virtual network connectivity requirements](#additional-virtual-network-connectivity-requirements).

#### Geo-replication peer port requirements

If you're using geo-replication between caches in Azure virtual networks, unblock ports 15000-15999 for the whole subnet in both inbound *and* outbound directions to both caches. With this configuration, all the replica components in the subnet can communicate directly with each other even if there's a future geo-failover.

#### Inbound port requirements

There are eight inbound port range requirements. Inbound requests in these ranges are either inbound from other services hosted in the same virtual network or internal to the Redis subnet communications.

| Ports | Direction | Transport protocol | Purpose | Local IP | Remote IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inbound |TCP |Client communication to Redis, Azure load balancing | (Redis subnet) | (Redis subnet), (Client subnet), AzureLoadBalancer <sup>1</sup> |
| 8443 |Inbound |TCP |Internal communications for Redis | (Redis subnet) |(Redis subnet) |
| 8500 |Inbound |TCP/UDP |Azure load balancing | (Redis subnet) | AzureLoadBalancer |
| 10221-10231 |Inbound |TCP |Client communication to Redis Clusters, internal communications for Redis | (Redis subnet) |(Redis subnet), AzureLoadBalancer, (Client subnet) |
| 13000-13999 |Inbound |TCP |Client communication to Redis Clusters, Azure load balancing | (Redis subnet) | (Redis subnet), (Client subnet), AzureLoadBalancer |
| 15000-15999 |Inbound |TCP |Client communication to Redis Clusters, Azure load balancing, and geo-replication | (Redis subnet) | (Redis subnet), (Client subnet), AzureLoadBalancer, (Geo-replica peer subnet) |
| 16001 |Inbound |TCP/UDP |Azure load balancing | (Redis subnet) | AzureLoadBalancer |
| 20226 |Inbound |TCP |Internal communications for Redis | (Redis subnet) |(Redis subnet) |

<sup>1</sup> You can use the service tag AzureLoadBalancer for Resource Manager or AZURE_LOADBALANCER for the classic deployment model for authoring the NSG rules.

#### Additional virtual network connectivity requirements

There are network connectivity requirements for Azure Cache for Redis that might not be initially met in a virtual network. Azure Cache for Redis requires all the following items to function properly when used within a virtual network:

* Outbound network connectivity to Azure Storage endpoints worldwide. Endpoints located in the same region as the Azure Cache for Redis instance and storage endpoints located in *other* Azure regions are included. Azure Storage endpoints resolve under the following DNS domains: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net*, and *file.core.windows.net*.
* Outbound network connectivity to *ocsp.digicert.com*, *crl4.digicert.com*, *ocsp.msocsp.com*, *mscrl.microsoft.com*, *crl3.digicert.com*, *cacerts.digicert.com*, *oneocsp.microsoft.com*, and *crl.microsoft.com*. This connectivity is needed to support TLS/SSL functionality.
* The DNS configuration for the virtual network must be capable of resolving all of the endpoints and domains mentioned in the earlier points. These DNS requirements can be met by ensuring a valid DNS infrastructure is configured and maintained for the virtual network.
* Outbound network connectivity to the following Azure Monitor endpoints, which resolve under the following DNS domains: *shoebox2-black.shoebox2.metrics.nsatc.net*, *north-prod2.prod2.metrics.nsatc.net*, *azglobal-black.azglobal.metrics.nsatc.net*, *shoebox2-red.shoebox2.metrics.nsatc.net*, *east-prod2.prod2.metrics.nsatc.net*, and *azglobal-red.azglobal.metrics.nsatc.net*.

### How can I verify that my cache is working in a virtual network?

>[!IMPORTANT]
>When you connect to an Azure Cache for Redis instance that's hosted in a virtual network, your cache clients must be in the same virtual network or in a virtual network with virtual network peering enabled within the same Azure region. Global virtual network peering isn't currently supported. This requirement applies to any test applications or diagnostic pinging tools. Regardless of where the client application is hosted, NSGs or other network layers must be configured such that the client's network traffic is allowed to reach the Azure Cache for Redis instance.
>

After the port requirements are configured as described in the previous section, you can verify that your cache is working by following these steps:

- [Reboot](cache-administration.md#reboot) all of the cache nodes. If all of the required cache dependencies can't be reached, as documented in [Inbound port requirements](cache-how-to-premium-vnet.md#inbound-port-requirements) and [Outbound port requirements](cache-how-to-premium-vnet.md#outbound-port-requirements), the cache won't be able to restart successfully.
- After the cache nodes have restarted, as reported by the cache status in the Azure portal, you can do the following tests:
  - Ping the cache endpoint by using port 6380 from a machine that's within the same virtual network as the cache, using [tcping](https://www.elifulkerson.com/projects/tcping.php). For example:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    If the `tcping` tool reports that the port is open, the cache is available for connection from clients in the virtual network.

  - Another way to test is to create a test cache client (which could be a simple console application using StackExchange.Redis) that connects to the cache and adds and retrieves some items from the cache. Install the sample client application onto a VM that's in the same virtual network as the cache. Then run it to verify connectivity to the cache.


### When I try to connect to my Azure Cache for Redis instance in a virtual network, why do I get an error stating the remote certificate is invalid?

When you try to connect to an Azure Cache for Redis instance in a virtual network, you see a certificate validation error such as this one:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

The cause could be that you're connecting to the host by the IP address. We recommend that you use the host name. In other words, use the following string:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Avoid using the IP address similar to the following connection string:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

If you're unable to resolve the DNS name, some client libraries include configuration options like `sslHost`, which is provided by the StackExchange.Redis client. This option allows you to override the host name used for certificate validation. For example:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### Can I use virtual networks with a standard or basic cache?

Virtual networks can only be used with Premium-tier caches.

### Why does creating an Azure Cache for Redis instance fail in some subnets but not others?

If you're deploying an Azure Cache for Redis instance to a virtual network, the cache must be in a dedicated subnet that contains no other resource type. If an attempt is made to deploy an Azure Cache for Redis instance to a Resource Manager virtual network subnet that contains other resources, such as Azure Application Gateway instances and Outbound NAT, the deployment will usually fail. You must delete existing resources of other types before you can create a new Azure Cache for Redis instance.

You must also have enough IP addresses available in the subnet.

### What are the subnet address space requirements?

Azure reserves some IP addresses within each subnet, and these addresses can't be used. The first and last IP addresses of the subnets are reserved for protocol conformance, along with three more addresses used for Azure services. For more information, see [Are there any restrictions on using IP addresses within these subnets?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

In addition to the IP addresses used by the Azure virtual network infrastructure, each Azure Cache for Redis instance in the subnet uses two IP addresses per cluster shard, plus additional IP addresses for additional replicas, if any. One additional IP address is used for the load balancer. A nonclustered cache is considered to have one shard.

### Do all cache features work when a cache is hosted in a virtual network?

When your cache is part of a virtual network, only clients in the virtual network can access the cache. As a result, the following cache management features don't work at this time:

* **Redis Console**: Because Redis Console runs in your local browser, which is usually on a developer machine that isn't connected to the virtual network, it can't then connect to your cache.

## Use ExpressRoute with Azure Cache for Redis

Customers can connect an [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuit to their virtual network infrastructure. In this way, they extend their on-premises network to Azure.

By default, a newly created ExpressRoute circuit doesn't perform forced tunneling (advertisement of a default route, 0.0.0.0/0) on a virtual network. As a result, outbound internet connectivity is allowed directly from the virtual network. Client applications can connect to other Azure endpoints, which includes an Azure Cache for Redis instance.

A common customer configuration is to use forced tunneling (advertise a default route), which forces outbound internet traffic to instead flow on-premises. This traffic flow breaks connectivity with Azure Cache for Redis if the outbound traffic is then blocked on-premises such that the Azure Cache for Redis instance isn't able to communicate with its dependencies.

The solution is to define one or more user-defined routes (UDRs) on the subnet that contains the Azure Cache for Redis instance. A UDR defines subnet-specific routes that will be honored instead of the default route.

If possible, use the following configuration:

* The ExpressRoute configuration advertises 0.0.0.0/0 and, by default, force tunnels all outbound traffic on-premises.
* The UDR applied to the subnet that contains the Azure Cache for Redis instance defines 0.0.0.0/0 with a working route for TCP/IP traffic to the public internet. For example, it sets the next hop type to *internet*.

The combined effect of these steps is that the subnet-level UDR takes precedence over the ExpressRoute forced tunneling, which ensures outbound internet access from the Azure Cache for Redis instance.

Connecting to an Azure Cache for Redis instance from an on-premises application by using ExpressRoute isn't a typical usage scenario because of performance reasons. For best performance, Azure Cache for Redis clients should be in the same region as the Azure Cache for Redis instance.

>[!IMPORTANT]
>The routes defined in a UDR *must* be specific enough to take precedence over any routes advertised by the ExpressRoute configuration. The following example uses the broad 0.0.0.0/0 address range and, as such, can potentially be accidentally overridden by route advertisements that use more specific address ranges.

>[!WARNING]
>Azure Cache for Redis isn't supported with ExpressRoute configurations that *incorrectly cross-advertise routes from the public peering path to the private peering path*. ExpressRoute configurations that have public peering configured receive route advertisements from Microsoft for a large set of Microsoft Azure IP address ranges. If these address ranges are incorrectly cross-advertised on the private peering path, the result is that all outbound network packets from the Azure Cache for Redis instance's subnet are incorrectly force-tunneled to a customer's on-premises network infrastructure. This network flow breaks Azure Cache for Redis. The solution to this problem is to stop cross-advertising routes from the public peering path to the private peering path.

Background information on UDRs is available in [Virtual network traffic routing](../virtual-network/virtual-networks-udr-overview.md).

For more information about ExpressRoute, see [ExpressRoute technical overview](../expressroute/expressroute-introduction.md).

## Next steps

Learn more about Azure Cache for Redis features.

* [Azure Cache for Redis Premium service tiers](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
