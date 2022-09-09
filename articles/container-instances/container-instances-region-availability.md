---
title: Resource availability by region
description: Availability of compute and memory resources for the Azure Container Instances service in different Azure regions.
ms.topic: article
ms.date: 12/17/2019
ms.author: danlep

---
# Resource availability for Azure Container Instances in Azure regions

This article details the availability of Azure Container Instances compute and memory resources in Azure regions. 

Values presented are the maximum resources available per deployment of a [container group](container-instances-container-groups.md). Values are current at time of publication. 

> [!NOTE]
> Container groups created within these resource limits are subject to availability within the deployment region. When a region is under heavy load, you may experience a failure when deploying instances. To mitigate such a deployment failure, try deploying instances with lower resource settings, or try your deployment at a later time.

For information about quotas and other limits in your deployments, see [Quotas and limits for Azure Container Instances](container-instances-quotas.md).

## Availability - General

The following regions and resources are available to container groups with Linux and [supported](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 2016-based containers.

| Regions | OS | Max CPU | Max Memory (GB) |
| -------- | -- | :---: | :-----------: |
| Brazil South, Canada Central, Central India, Central US, East Asia, East US, East US 2, North Europe, South Central US, Southeast Asia, South India, UK South, West Europe, West US, West US 2 | Linux | 4 | 16 |
| Australia East, Japan East | Linux | 2 | 8 |
| North Central US | Linux | 2 | 3.5 |
| Brazil South, Japan East, West Europe | Windows | 4 | 16 |
| East US, West US | Windows | 4 | 14 |
| Australia East, Canada Central, Central India, Central US, East Asia, East US 2,  North Central US, North Europe, South Central US, Southeast Asia, South India, UK South, West US 2 | Windows | 2 | 3.5 |

## Availability - Windows Server 2019 LTSC, 1809 deployments (preview)

The following regions and resources are available to container groups with Windows Server 2019-based containers (preview).

| Regions | OS | Max CPU | Max Memory (GB) |
| -------- | -- | :---: | :-----------: |
| Australia East, Brazil South, Canada Central, Central India, Central US, East Asia, East US, Japan East, North Central US, North Europe, South Central US, Southeast Asia, South India, UK South, West Europe | Windows | 4 | 16 |
| East US 2, West US 2 | Windows | 2 | 3.5 |


## Availability - Virtual network deployment (preview)

The following regions and resources are available to a container group deployed in an [Azure virtual network](container-instances-vnet.md) (preview).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## Availability - GPU resources (preview)

The following regions and resources are available to a container group deployed with [GPU resources](container-instances-gpu.md) (preview).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## Next steps

Let the team know if you'd like to see additional regions or increased resource availability at [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

For information on troubleshooting container instance deployment, see [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md).
