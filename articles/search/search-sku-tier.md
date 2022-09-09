---
title: Choose a pricing tier
titleSuffix: Azure Cognitive Search
description: 'Learn about the pricing tiers (or SKUs) for Azure Cognitive Search. A search service can be provisioned at these tiers: Free, Basic, and Standard. Standard is available in various resource configurations and capacity levels.'

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2 
---

# Choose a pricing tier for Azure Cognitive Search

Part of [creating a search service](search-create-service-portal.md) means choosing a pricing tier (or SKU) that's fixed for the lifetime of the service. Prices - or the estimated monthly cost of running the service - are shown in the portal's **Select Pricing Tier** page when you create the service. If you're provisioning through PowerShell or Azure CLI instead, the tier is specified through the **`-Sku`** parameter, and you should check [service pricing](https://azure.microsoft.com/pricing/details/search/) to learn about estimated costs.

The tier you select determines:

+ Maximum number of indexes and other objects allowed on the service
+ Size and speed of partitions (physical storage)
+ Billable rate as a fixed monthly cost, but also an incremental cost if you add capacity

In a few instances, the tier you choose determines the availability of [premium features](#premium-features).

> [!NOTE]
> Looking for information about "Azure SKUs"? Start with [Azure pricing](https://azure.microsoft.com/pricing/) and then scroll down for links to per-service pricing pages.

## Tier descriptions

Tiers include **Free**, **Basic**, **Standard**, and **Storage Optimized**. Standard and Storage Optimized are available with several configurations and capacities. The following screenshot from Azure portal shows the available tiers, minus pricing (which you can find in the portal and on the [pricing page](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Pricing tier chart" border="true":::

**Free** creates a limited search service for smaller projects, like running tutorials and code samples. Internally, system resources are shared among multiple subscribers. You cannot scale a free service or run significant workloads.

**Basic** and **Standard** are the most commonly used billable tiers, with **Standard** being the default because it gives you more flexibility in scaling for workloads. With dedicated resources under your control, you can deploy larger projects, optimize performance, and increase capacity.

Some tiers are designed for certain types of work. For example, **Standard 3 High Density (S3 HD)** is a *hosting mode* for S3, where the underlying hardware is optimized for a large number of smaller indexes and is intended for multitenancy scenarios. S3 HD has the same per-unit charge as S3, but the hardware is optimized for fast file reads on a large number of smaller indexes.

**Storage Optimized** tiers offer larger storage capacity at a lower price per TB than the Standard tiers. The primary tradeoff is higher query latency, which you should validate for your specific application requirements. To learn more about the performance considerations of this tier, see [Performance and optimization considerations](search-performance-optimization.md).

You can find out more about the various tiers on the [pricing page](https://azure.microsoft.com/pricing/details/search/), in the [Service limits in Azure Cognitive Search](search-limits-quotas-capacity.md) article, and on the portal page when you're provisioning a service.

<a name="premium-features"></a>

## Feature availability by tier

Most features are available on all tiers, including the free tier. In a few cases, the tier you choose will impact your ability to implement a feature. The following table describes feature constraints that are related to service tier.

| Feature | Limitations |
|---------|-------------|
| [indexers](search-indexer-overview.md) | Indexers are not available on S3 HD.  |
| [AI enrichment](search-security-manage-encryption-keys.md) | Runs on the Free tier but not recommended. |
| [Managed or trusted identities for outbound (indexer) access](search-howto-managed-identities-data-sources.md) | Not available on the Free tier.|
| [Customer-managed encryption keys](search-security-manage-encryption-keys.md) | Not available on the Free tier. |
| [IP firewall access](service-configure-firewall.md) | Not available on the Free tier. |
| [Private endpoint (integration with Azure Private Link)](service-create-private-endpoint.md) | For inbound connections to a search service, not available on the Free tier. For outbound connections by indexers to other Azure resources, not available on Free or S3 HD. For indexers that use skillsets, not available on Free, Basic, S1, or S3 HD.| 
| [Availability Zones](search-performance-optimization.md) | Not available on the Free tier and Basic tier. |

Resource-intensive features might not work well unless you give it sufficient capacity. For example, [AI enrichment](cognitive-search-concept-intro.md) has long-running skills that time out on a Free service unless the dataset is small.

## Upper limits

Tiers determine the  maximum storage of the service itself, as well as the maximum number of indexes, indexers, data sources, skillsets, and synonym maps that you can create. For a full break out of all limits, see [Service limits in Azure Cognitive Search](search-limits-quotas-capacity.md). 

## Partition size and speed

Tier pricing includes details about per-partition storage that ranges from 2 GB for Basic, up to 2 TB for Storage Optimized (L2) tiers. Other hardware characteristics, such as speed of operations, latency, and transfer rates, are not published, but tiers that are designed for specific solution architectures are built on hardware that has the features to support those scenarios. For more information about partitions, see [Estimate and manage capacity](search-capacity-planning.md) and [Scale for performance](search-performance-optimization.md).

## Billing rates

Tiers have different billing rates, with higher rates for tiers that run on more expensive hardware or provide more expensive features. The per-tier billing rate can be found in the [Azure pricing pages](https://azure.microsoft.com/pricing/details/search/) for Azure Cognitive Search.

Once you create a service, the billing rate becomes both a *fixed cost* of running the service around the clock, and an *incremental cost* if you choose to add more capacity.

Search services are allocated computing resources in the form of *partitions* (for storage), and *replicas* (instances of the query engine). Initially, a service is created with one of each, and the billing rate is inclusive of both resources. However, if you scale capacity, the costs go up or down in increments of the billable rate.

The following example provides an illustration. Assume a hypothetical billing rate of $100 per month. If you keep the search service at its initial capacity of one partition and one replica, then $100 is what you can expect to pay at the end of the month. However, if you add two more replicas to achieve high availability, the monthly bill increases to $300 ($100 for the first replica-partition pair, followed by $200 for the two replicas).

This pricing model is based on the concept of applying the billing rate to the number *search units* (SU) used by a search service. All services are initially provisioned at one SU, but you can increase the SUs by adding either partitions or replicas to handle larger workloads. For more information, see [How to estimate costs of a search service](search-sku-manage-costs.md).

## Next steps

The best way to choose a pricing tier is to start with a least-cost tier, and then allow experience and testing inform your decision to keep the service or create a new one at a higher tier. For next steps, we recommend that you create a search service at a tier that can accommodate the level of testing you propose to do, and then review the following guidance for recommendations on estimating cost and capacity.

+ [Create a search service](search-create-service-portal.md)
+ [Estimate costs](search-sku-manage-costs.md)
+ [Estimate capacity](search-sku-manage-costs.md)