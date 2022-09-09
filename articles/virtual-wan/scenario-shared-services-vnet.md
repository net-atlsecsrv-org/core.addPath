---
title: 'Scenario: Route to Shared Services VNets'
titleSuffix: Azure Virtual WAN
description: Scenarios for routing - set up routes to access a Shared Service VNet with a workload that you want every VNet and Branch to access.
services: virtual-wan
author: cherylmc

ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit

---
# Scenario: Route to Shared Services VNets

When working with Virtual WAN virtual hub routing, there are quite a few available scenarios. In this scenario, the goal is to set up routes to access a **Shared Service** VNet with workloads that you want every VNet and Branch (VPN/ER/P2S) to access. Examples of these shared workloads might include Virtual Machines with services like Domain Controllers or File Shares, or Azure services exposed internally through [Azure Private Endpoints](../private-link/private-endpoint-overview.md).

For more information about virtual hub routing, see [About virtual hub routing](about-virtual-hub-routing.md).

## <a name="design"></a>Design

We can use a connectivity matrix to summarize the requirements of this scenario. In the matrix, each cell describes whether a Virtual WAN connection (the "From" side of the flow, the row headers in the table) learns a destination prefix (the "To" side of the flow, the column headers in italics in the table) for a specific traffic flow. An "X" means that connectivity is provided by Virtual WAN:

**Connectivity matrix**

| From             | To:   |*Isolated VNets*|*Shared VNet*|*Branches*|
|---|---|---|---|---|
|**Isolated VNets**|&#8594;|                |        X        |       X      |
|**Shared VNets**  |&#8594;|       X        |        X        |       X      |
|**Branches**      |&#8594;|       X        |        X        |       X      |

Similar to the [Isolated VNet scenario](scenario-isolate-vnets.md), this connectivity matrix gives us two different row patterns, which translate to two route tables (the shared services VNets and the branches have the same connectivity requirements). Virtual WAN already has a Default route table, so we will need another custom route table, which we will call **RT_SHARED** in this example.

VNets will be associated to the **RT_SHARED** route table. Because they need connectivity to branches and to the shared service VNets, the shared service VNet and branches will need to propagate to **RT_SHARED** (otherwise the VNets would not learn the branch and shared VNet prefixes). Because the branches are always associated to the Default route table, and the connectivity requirements are the same for shared services VNets, we will associate the shared service VNets to the Default route table too.

As a result, this is the final design:

* Isolated virtual networks:
  * Associated route table: **RT_SHARED**
  * Propagating to route tables: **Default**
* Shared Services virtual networks:
  * Associated route table: **Default**
  * Propagating to route tables: **RT_SHARED** and **Default**
* Branches:
  * Associated route table: **Default**
  * Propagating to route tables: **RT_SHARED** and **Default**

> [!NOTE]
> If your Virtual WAN is deployed over multiple regions, you will need to create the **RT_SHARED** route table in every hub, and routes from each shared services VNet and branch connection need to be propagated to the route tables in every virtual hub using propagation labels.

For more information about virtual hub routing, see [About virtual hub routing](about-virtual-hub-routing.md).

## <a name="workflow"></a>Workflow

To configure the scenario, consider the following steps:

1. Identify the **Shared Services** VNet.
2. Create a custom route table. In the example, we refer to the route table as **RT_SHARED**. For steps to create a route table, see [How to configure virtual hub routing](how-to-virtual-hub-routing.md). Use the following values as a guideline:

   * **Association**
     * For **VNets *except* the Shared Services VNet**, select the VNets to isolate. This will imply that all these VNets (except the shared services VNet) will be able to reach destination based on the routes of RT_SHARED route table.

   * **Propagation**
      * For **Branches**, propagate routes to this route table, in addition to any other route tables you may have already selected. Because of this step, the RT_SHARED route table will learn routes from all branch connections (VPN/ER/User VPN).
      * For **VNets**, select the **Shared Services VNet**. Because of this step, RT_SHARED route table will learn routes from the Shared Services VNet connection.

This will result in the routing configuration shown in the following figure:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Shared services VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## Next steps

* For more information about Virtual WAN, see the [FAQ](virtual-wan-faq.md).
* For more information about virtual hub routing, see [About virtual hub routing](about-virtual-hub-routing.md).
