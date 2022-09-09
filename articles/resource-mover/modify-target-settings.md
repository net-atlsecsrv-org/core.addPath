---
title: Modify target settings when moving Azure VMs between regions with Azure Resource Mover
description: Learn how to modify target settings when moving Azure VMs between regions with Azure Resource Mover.
manager: evansma
author: rayne-wiselman 
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
#Customer intent: As an Azure admin,  I want modify target settings when moving resources to another region.

---
# Modify target settings

This article describes how to modify target settings, when moving resources between Azure regions with [Azure Resource Mover](overview.md).


## Modify VM settings

When moving Azure VMs and associated resources, you can modify the target settings. 

- We recommend that you only change target settings after the move collection is validated.
- We recommend that you modify settings before preparing the resources, because some target properties might be unavailable for edit after prepare is complete.

However:
- If you're moving the source resource, you can usually modify target settings until you start the initiate move process.
- If you assign an existing resource in the source region, you can modify target settings until the move commit is complete.

### Settings you can modify

Configuration settings you can modify are summarized in the table.

**Resource** | **Options** 
--- | --- | --- 
**VM Name** | Options:<br/><br/> - Create a new VM with the same name in the target region.<br/><br/> - Create a new VM with a different name in the target region.<br/><br/> - Use an existing VM in the target region.<br/><br/> If you create a new VM, with the exception of the settings you modify, the new target VM is assigned the same settings as the source.
**VM availability zone** | The availability zone in which the target VM will be placed. This can be marked **NA** if you don’t want to change the source settings, or if you don’t want to place the VM in an availability zone.
**VM SKU** | The [VM type](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (available in the target region) that will be used for the target VM.<br/><br/> The selected target VM shouldn't be smaller than the source VM.
**Networking resources** | Options for virtual networks (VNets)/network security groups/network interfaces:<br/><br/> - Create a new resource with the same name in the target region.<br/><br/> - Create a new resource with a different name in the target region.<br/><br/> - Use an existing networking resource in the target region.<br/><br/> If you create a new target resource, with the exception of the settings you modify, it's assigned the same settings as the source resource.
**Public IP address name** | Specify the name.
**Public IP address SKU** | Specify the [SKU](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku).
**Public IP address zone** | Specify the [zone](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#standard) for standard public IP addresses.<br/><br/> If you want it to be zone redundant, enter as **Regional**.
**Load balancer name** | Specify the name.
**Load balancer SKU** | Basic or Standard. We recommend using Standard.
**Load balancer zone** | Specify a zone for the load balancer. <br/><br/> If you want it to be zone redundant, enter as **Regional**.
**Resource dependencies** | Options for each dependency:<br/><br/>- The resource uses source dependent resources that will move to the target region.<br/><br/> - The resource uses different dependent resources located in the target region. In this case, you can choose from any similar resources in the target region.

### Edit VM target settings

If you don't want to dependent resources from the source region to the target, you have a couple of other options:

- Create a new resource in the target region. Unless you specify different settings, the new resource will have the same settings as the source resource.
- Use an existing resource in the target region.

Exact behavior depends on the resource type. [Learn more](modify-target-settings.md) about modifying target settings.

You modify the target settings for a resource using the **Target configuration** entry in the resource move collection. 

To modify a setting: 

1. In the **Across regions** page > **Target configuration** column, click the link for the resource entry.
2. In **Configuration settings**, you can create a new VM in the target region.
3. Assign a new availability zone, availability set, or SKU to the target VM. **Availability zone** and **SKU**.

Changes are only made for the resource you're editing. You need to update any dependent resource separately.


## Modify SQL settings

When moving Azure SQL Database resources, you can modify the target settings for the move. 

- For SQL database:
    - We recommend that you modify target configuration settings before you prepare them for move.
    - You can modify the settings for the target database, and zone redundancy for the database.
- For elastic pools:
    -  You can modify the target configuration anytime before initiating the move.
    - You can modify the target elastic pool, and zone redundancy for the pool. 

### SQL settings you can modify

**Setting** | **SQL database** | **Elastic pool**
--- | --- | ---
**Name** | Create a new database with the same name in the target region.<br/><br/> Create a new database with a different name in the target region.<br/><br/> Use an existing database in the target region. | Create a new elastic pool with the same name in the target region.<br/><br/> Create a new elastic pool with a different name in the target region.<br/><br/> Use an existing elastic pool in the target region.
**Zone redundancy** | To move from a region that supports zone redundancy to a region that doesn't, type **Disable** in the zone setting.<br/><br/> To move from a region that doesn't support zone redundancy to a region that does, type **Enable** in the zone setting. | To move from a region that supports zone redundancy to a region that doesn't, type **Disable** in the zone setting.<br/><br/> To move from a region that doesn't support zone redundancy to a region that does, type **Enable** in the zone setting.

### Edit SQL target settings

You modify the target settings for a Azure SQL Database resource as follows: 

1. In **Across regions**, for the resource you want to modify, click the **Target configuration** entry.
2. In **Configuration settings**, specify the target settings summarized in the table above.

## Next steps

[Move an Azure VM](tutorial-move-region-virtual-machines.md) to another region.
