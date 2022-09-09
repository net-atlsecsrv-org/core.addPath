---
title: Container image storage 
description: Details on how your container images and other artifacts are stored in Azure Container Registry, including security, redundancy, and capacity.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
---

# Container image storage in Azure Container Registry

Every [Basic, Standard, and Premium](container-registry-skus.md) Azure container registry benefits from advanced Azure storage features including encryption-at-rest. The following sections describe the features and limits of image storage in Azure Container Registry (ACR).

## Encryption-at-rest

All container images and other artifacts in your registry are encrypted at rest. Azure automatically encrypts an image before storing it, and decrypts it on-the-fly when you or your applications and services pull the image. Optionally apply an extra encryption layer with a [customer-managed key](container-registry-customer-managed-keys.md).

## Regional storage

Azure Container Registry stores data in the region where the registry is created, to help customers meet data residency and compliance requirements.

To help guard against datacenter outages, some regions offer [zone redundancy](zone-redundancy.md), where data is replicated across multiple datacenters in a particular region.

Customers who wish to have their data stored in multiple regions for better performance across different geographies or who wish to have resiliency in the event of a regional outage should enable [geo-replication](container-registry-geo-replication.md).

## Geo-replication

For scenarios requiring high-availability assurance, consider using the [geo-replication](container-registry-geo-replication.md) feature of Premium registries. Geo-replication helps guard against losing access to your registry in the event of a regional failure. Geo-replication provides other benefits, too, like network-close image storage for faster pushes and pulls in distributed development or deployment scenarios.

## Zone redundancy

To create a resilient and high-availability Azure container registry, optionally enable [zone redundancy](zone-redundancy.md) in select Azure regions. A feature of the Premium service tier, zone redundancy uses Azure [availability zones](../availability-zones/az-overview.md) to replicate your registry to a minimum of three separate zones in each enabled region. Combine geo-replication and zone redundancy to enhance both the reliability and performance of a registry. 

## Scalable storage

Azure Container Registry allows you to create as many repositories, images, layers, or tags as you need, up to the [registry storage limit](container-registry-skus.md#service-tier-features-and-limits). 

High numbers of repositories and tags can affect the performance of your registry. Periodically delete unused repositories, tags, and images as part of your registry maintenance routine, and optionally set a [retention policy](container-registry-retention-policy.md) for untagged manifests. Deleted registry resources such as repositories, images, and tags *cannot* be recovered after deletion. For more information about deleting registry resources, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## Storage cost

For full details about pricing, see [Azure Container Registry pricing][pricing].

## Next steps

For more information about Basic, Standard, and Premium container registries, see [Azure Container Registry service tiers](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
