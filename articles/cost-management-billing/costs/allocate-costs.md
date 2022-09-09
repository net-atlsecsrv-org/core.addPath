---
title: Allocate Azure costs
description: This article explains how create cost allocation rules to distribute costs of subscriptions, resource groups, or tags to others.
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
---

# Create and manage Azure cost allocation rules (Preview)

Large enterprises often have Azure services or resources that are centrally managed but are utilized by different internal departments or business units. Typically, the centrally managing team wants to reallocate the cost of the shared services back out to the internal departments or organizational business units who are actively using the services. This article helps you understand and use cost allocation in Azure Cost Management.

With cost allocation, you can reassign or distribute the costs of shared services from subscriptions, resource groups or tags to other subscriptions, resource groups or tags in your organization. Cost allocation shifts costs of the shared services to another subscription, resource groups, or tags owned by the consuming internal departments or business units. In other words, cost allocation helps to manage and show _cost accountability_ from one place to another.

Cost allocation doesn't affect your billing invoice. Billing responsibilities aren't changed. The primary purpose of cost allocation is to help you charge back costs to others. All chargeback processes happen in your organization outside of Azure. Cost allocation helps you charge back costs by showing them as they're reassigned or distributed.

Allocated costs are shown in cost analysis. They're shown as additional items associated with the targeted subscriptions, resource groups, or tags that you specify when you create a cost allocation rule.

> [!NOTE]
> Azure Cost Management's cost allocation feature is currently in public preview. Some features in Cost Management might not be supported or might have limited capabilities.

## Prerequisites

- Cost allocation currently only supports customers with a [Microsoft Customer Agreement](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) or an [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- To create or manage a cost allocation rule, you must use an Enterprise Administrator account for [Enterprise Agreements](../manage/understand-ea-roles.md). Or you must be a [Billing account](../manage/understand-mca-roles.md) owner for Microsoft Customer Agreements.

## Create a cost allocation rule

1. Sign in to the Azure portal at [https://portal.azure.com](https://portal.azure.com/).
2. Navigate to **Cost Management + Billing** > **Cost Management**.
3. Under **Settings** > **Configuration**, select **Cost allocation (Preview)**.
4. Ensure that the correct the EA enrollment or billing account is selected.
5. Select **+Add**.
6. Enter descriptive text for the cost allocation rule name.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Example showing creating a rule name" lightbox="./media/allocate-costs/rule-name.png" :::

The rule's evaluation start date is used to generate the prefilled cost allocation percentages.

1. Select **Add sources** and then select either subscriptions, resource groups, or tags to choose costs to distribute.
2. Select **Add targets** and then select either subscriptions, resource groups, or tags that will receive the allocated costs.
3. If you need to create additional cost allocation rules, repeat this process.

## Configure the allocation percentage

Configure the allocation percentage to define how costs are proportionally divided between the specified targets. You can manually define whole number percentages to create an allocation rule. Or you can split the costs proportionally based on the current usage of the compute, storage, or network across the specified targets.

When distributing costs by compute cost, storage cost, or network cost, the proportional percentage is derived by evaluating the selected target's costs. The costs are associated with the resource type for the current billing month.

When distributing costs proportional to total cost, the proportional percentage is allotted by the sum or total cost of the selected targets for the current billing month.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Example showing allocation percentage" lightbox="./media/allocate-costs/cost-distribution.png" :::

Once set, the prefilled percentages defined are fixed. They're used for all ongoing allocations. The percentages change only when the rule is manually updated.

1. Select one of the following options in the **Prefill percentage to** list.
    - **Distribute evenly** – Each of the targets receives an even percentage proportion of the total cost.
    - **Total cost** – Creates a ratio proportional to the targets based on their total cost. The ratio is used to distribute the costs from the selected sources.
    - **Compute cost** - Creates a ratio proportional to the targets based on their Azure compute cost (resource types in the [Microsoft.Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/allversions) namespace.The ratio is used to distribute the costs from the selected sources.
    - **Storage cost** - Creates a ratio proportional to the targets based on their Azure storage cost (resource types in the [Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) namespace). The ratio is used to distribute the costs from the selected sources.
    - **Network cost** - Creates a ratio proportional to the targets based on their Azure network cost (resource types in the [Microsoft.Network](https://docs.microsoft.com/azure/templates/microsoft.network/allversions) namespace). The ratio is used to distribute the costs from the selected sources.
    - **Custom** – Allows for a whole number percentage to be manually specified. The specified total must equal 100%.
1. When the rule is configured, select **Create**.

The allocation rule starts processing. When the rule is active, all the selected source's costs are allocated to the specified targets.

> [!NOTE] 
> New rule processing can take up to two hours before it completes and is active.

## Verify the cost allocation rule

When the cost allocation rule is active, costs from the selected sources are distributed to the specified allocation targets. Use the following information to verify that cost is properly allocated to targets.

### View cost allocation for a subscription

You view the impact of the allocation rule in cost analysis. In the Azure portal, go to [Subscriptions](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Select a subscription in the list that's targeted by an active cost allocation rule. Then select **Cost analysis** in the menu. In Cost analysis, select **Group by** and then select **Cost allocation**. The resulting view shows a quick cost breakdown generated by the subscription. Costs allocated to the subscription are also shown, like the following image.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Example showing cost breakdown" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### View cost allocation for a resource group

Use a similar process the impact of a cost allocation rule for a resource group. In the Azure portal, go to [Resource groups](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Select a resource group in the list that's targeted by an active cost allocation rule. Then select **Cost analysis** in the menu. In Cost analysis, select **Group by** and then select **Cost allocation**. The view shows you a quick cost breakdown generated by the resource group. Cost allocated to the resource group are also shown.

### View cost allocation for tags

In the Azure portal, navigate to **Cost Management + Billing** > **Cost Management** > **Cost analysis**. In Cost analysis, select **Add filter**. Select **Tag**, choose the tag key, and tag values that have cost allocated to them.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Example showing costs for tagged items" lightbox="./media/allocate-costs/tagged-costs.png" :::

## Edit an existing cost allocation rule

You can edit a cost allocation rule to change the source or the target or if you want to update the prefilled percentage for either compute, storage, or network options. Edit the rules in the same way you create them. Modifying existing rules can take up to two hours to reprocess.

## Frequently asked questions (FAQ)

The following sections contain common questions people have about cost allocation.

### What are the current limitations with cost allocation in public preview?
<a name="limitations"></a>

Currently, cost allocation is supported in Cost Management by Cost analysis, budgets, and forecast views. Allocated costs are also shown in the subscriptions list and on the Subscriptions overview page.

The following items are currently unsupported by the cost allocation public preview:

- Scheduled [Exports](tutorial-export-acm-data.md)
- Data exposed by the [Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API
- Billing subscriptions area
- [Cost Management Power BI App](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop connector](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)

### Are costs factored into budgets and forecast views?
<a name="budgets-forecast"></a>

Yes. Allocated costs are factored into and supported by budgets and forecasts. Budget and forecast views show costs allocated to them, as configured by cost allocation rules.

### If a cost allocation rule is deleted, what happens?
<a name="delete-rule"></a>

When a cost allocation rule is deleted, all open and current billing month costs being allocated to the targets are removed. If the cost allocation rule has existed for several months, the historical previous months of allocation data remain as originally set by the allocation rule.

### Why is an enrollment admin or a billing account admin needed to create cost allocation rules?
<a name="why-admin"></a>

Cost allocation rules are created at either the enrollment scope (Enterprise Agreement) or the Billing account scope (Microsoft Customer Agreement). Permissions to make changes at these scopes requires billing administrator privileges.

### Why are sources and targets limited to 25 per rule?
<a name="source-target-rule-limit"></a>

The limit is a preview limitation to ensure good cost allocation performance and scalability. The limits will likely increase or get removed when cost allocation transitions to general availability (GA).

### What can happen if cost allocation rules (sources/targets) overlap?
<a name="rule-overlap"></a>

Rules that have either overlapping sources or overlapping targets aren't recommended. Cost Allocation rules are applied in order by their creation date, so if any cost allocation rules overlap the allocation rule with the earliest creation date will take precedence.

## Next steps

- Create or update allocation rules using the [Cost allocation Rest API](https://go.microsoft.com/fwlink/?linkid=2135004)
- Learn more about [How to optimize your cloud investment with Azure Cost Management](cost-mgt-best-practices.md)