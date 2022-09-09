---
title: High availability for Azure Cache for Redis
description: Learn about Azure Cache for Redis high availability features and options
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: yegu

---
# High availability for Azure Cache for Redis

Azure Cache for Redis has built-in high availability. The goal of its high availability architecture is to ensure that your managed Redis instance is functioning even when its underlying virtual machines (VMs) are impacted by planned or unplanned outages. It delivers much greater percentage rates than what's attainable by hosting Redis on a single VM.

Azure Cache for Redis implements high availability by using multiple VMs, called *nodes*, for a cache. It configures these nodes such that data replication and failover happen in coordinated manners. It also orchestrates maintenance operations such as Redis software patching. Various high availability options are available in the Standard and Premium tiers:

| Option | Description | Availability | Standard | Premium |
| ------------------- | ------- | ------- | :------: | :---: |
| [Standard replication](#standard-replication)| Dual-node replicated configuration in a single datacenter or availability zone (AZ), with automatic failover | 99.9% |✔|✔|
| [Multiple replicas](#multiple-replicas) | Multi-node replicated configuration in one or more AZs, with automatic failover | 99.95% (with zone redundancy) |-|✔|
| [Zone redundancy](#zone-redundancy) | Multi-node replicated configuration across AZs, with automatic failover | 99.95% (with multiple replicas) |-|✔|
| [Geo-replication](#geo-replication) | Linked cache instances in two regions, with user-controlled failover | 99.9% (for a single region) |-|✔|

## Standard replication

An Azure Cache for Redis in the Standard or Premium tier runs on a pair of Redis servers by default. The two servers are hosted on dedicated VMs. Open-source Redis allows only one server to handle data write requests. This server is the *primary* node, while the other *replica*. After it provisions the server nodes, Azure Cache for Redis assigns primary and replica roles to them. The primary node usually is responsible for servicing write as well as read requests from Redis clients. On a write operation, it commits a new key and a key update to its internal memory and replies immediately to the client. It forwards the operation to the replica asynchronously.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Data replication setup":::
   
>[!NOTE]
>Normally, a Redis client communicates with the primary node in a Redis cache for all read and write requests. Certain Redis clients can be configured to read from the replica node.
>
>

If the primary node in a Redis cache is unavailable, the replica will promote itself to become the new primary automatically. This process is called a *failover*. The replica will wait for sufficiently long time before taking over in case that the primary node recovers quickly. When a failover happens, Azure Cache for Redis provisions a new VM and joins it to the cache as the replica node. The replica performs a full data synchronization with the primary so that it has another copy of the cache data.

A primary node can go out of service as part of a planned maintenance activity such as Redis software or operating system update. It also can stop working because of unplanned events such as failures in underlying hardware, software, or network. [Failover and patching for Azure Cache for Redis](cache-failover.md) provides a detailed explanation on types of Redis failovers. An Azure Cache for Redis will go through many failovers during its lifetime. The high availability architecture is designed to make these changes inside a cache as transparent to its clients as possible.

## Multiple replicas

>[!NOTE]
>This is available as a preview.
>
>

Azure Cache for Redis allows additional replica nodes in the Premium tier. A [multi-replica cache](cache-how-to-multi-replicas.md) can be configured with up to three replica nodes. Having more replicas generally improves resiliency because of the additional nodes backing up the primary. Even with more replicas, an Azure Cache for Redis instance still can be severely impacted by a datacenter- or AZ-wide outage. You can increase cache availability by using multiple replicas in conjunction with [zone redundancy](#zone-redundancy).

## Zone redundancy

>[!NOTE]
>This is available as a preview.
>
>

Azure Cache for Redis supports zone redundant configurations in the Premium tier. A [zone redundant cache](cache-how-to-zone-redundancy.md) can place its nodes across different [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) in the same region. It eliminates datacenter or AZ outage as a single point of failure and increases the overall availability of your cache.

The following diagram illustrates the zone redundant configuration:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Zone redundancy setup":::
   
Azure Cache for Redis distributes nodes in a zone redundant cache in a round-robin manner over the AZs you've selected. It also determines which node will serve as the primary initially.

A zone redundant cache provides automatic failover. When the current primary node is unavailable, one of the replicas will take over. Your application may experience higher cache response time if the new primary node is located in a different AZ. AZs are geographically separated. Switching from one AZ to another alters the physical distance between where your application and cache are hosted. This change impacts round-trip network latencies from your application to the cache. The extra latency is expected to fall within an acceptable range for most applications. We recommend that you test your application to ensure that it can perform well with a zone-redundant cache.

## Geo-replication

Geo-replication is designed mainly for disaster recovery. It gives you the ability to configure an Azure Cache for Redis instance, in a different Azure region, to back up your primary cache. [Set up geo-replication for Azure Cache for Redis](cache-how-to-geo-replication.md) gives a detailed explanation on how geo-replication works.

## Next steps

Learn more about how to configure Azure Cache for Redis high-availability options.

* [Azure Cache for Redis Premium service tiers](cache-overview.md#service-tiers)
* [Add replicas to Azure Cache for Redis](cache-how-to-multi-replicas.md)
* [Enable zone redundancy for Azure Cache for Redis](cache-how-to-zone-redundancy.md)
* [Set up geo-replication for Azure Cache for Redis](cache-how-to-geo-replication.md)
