---
title: Plan and manage costs for Azure Cosmos DB
description: Learn how to plan for and manage costs for Azure Cosmos DB by using cost analysis in Azure portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
---

# Plan and manage costs for Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

This article describes how you can plan and manage costs for Azure Cosmos DB. First, you use the Azure Cosmos DB capacity calculator to estimate your workload cost before you create any resources. Later you can review the estimated cost and start creating your resources.

After you've started using Azure Cosmos DB resources, use the Cost Management features to set budgets and monitor costs. You can also review forecasted costs and identify spending trends to identify areas where you might want to act. The costs for Azure Cosmos DB are only a portion of the monthly costs in your Azure bill. Although this article explains how to plan for and manage costs for Azure Cosmos DB, you're billed for all Azure services and resources used in your Azure subscription, including the third-party services.

## Prerequisites

### Provisioned throughput or serverless

Azure Cosmos DB supports two types of capacity modes: [provisioned throughput](set-throughput.md) and [serverless](serverless.md). The way you get charged for your Azure Cosmos DB usage varies a lot between these two modes, so it's important to choose the one that works best for your workload. See the [how to choose between provisioned throughput and serverless](throughput-serverless.md) article for guidance and recommendations on how to make this choice.

### Cost analysis

Cost analysis in Cost Management supports most Azure account types, but not all of them. To view the full list of supported account types, see [Understand Cost Management data](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). To view cost data, you need at least read access for an Azure account. For information about assigning access to Azure Cost Management data, see [Assign access to data](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## Estimating provisioned throughput costs before using Azure Cosmos DB

If you plan to use Azure Cosmos DB in provisioned throughput mode, use the [Azure Cosmos DB capacity calculator](https://cosmos.azure.com/capacitycalculator/) to estimate costs before you create the resources in an Azure Cosmos account. The capacity calculator is used to get an estimate of the required throughput and cost of your workload. Configuring your Azure Cosmos databases and containers with the right amount of provisioned throughput, or [Request Units (RU/s)](request-units.md), for your workload is essential to optimize the cost and performance. You have to input details such as API type, number of regions, item size, read/write requests per second, total data stored to get a cost estimate. To learn more about the capacity calculator, see the [estimate](estimate-ru-with-capacity-planner.md) article.

The following screenshot shows the throughput and cost estimation by using the capacity calculator:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Cost estimate in Azure Cosmos DB capacity calculator":::

## <a id="estimating-serverless-costs"></a> Estimating serverless costs before using Azure Cosmos DB

If you plan to use Azure Cosmos DB in serverless mode, you need to estimate how many [Request Units](request-units.md) and GB of storage you may consume on a monthly basis. You can estimate the required amount of Request Units by evaluating the number of database operations that would be issued in a month, and multiply their amount by their corresponding RU cost. The following table lists estimated RU charges for common database operations:

| Operation | Estimated cost | Notes |
| --- | --- | --- |
| Create an item | 5 RUs | Average cost for a 1 KB item with less than 5 properties to index |
| Update an item | 10 RUs | Average cost for a 1 KB item with less than 5 properties to index |
| Read an individual item by its ID and partition key (point-read) | 1 RU | Average cost for a 1 KB item |
| Delete an item | 5 RUs | |
| Execute a query | 10 RUs | Average cost for a query that takes full advantage of [indexing](index-overview.md) and returns 100 results or less |

> [!IMPORTANT] 
> Pay attention to the Notes from the table above. For a more accurate estimation of the actual costs of your operations, you can use the [Azure Cosmos DB Emulator](local-emulator.md) and [measure the exact RU cost of your operations](find-request-unit-charge.md). Although the Azure Cosmos DB Emulator doesn't support serverless, it reports a standard RU charge for database operations and can be used for this estimation.

Once you have computed the total number of Request Units and GB of storage you're likely to consume over a month, the following formula will return your cost estimate: **([Number of Request units] / 1,000,000 * $0.25) + ([GB of storage] * $0.25)**.

> [!NOTE]
> The costs shown in the previous example are for demonstration purposes only. See the [pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/) for the latest pricing information.

## Review estimated costs in the Azure portal

As you start using Azure Cosmos DB resources from Azure portal, you can see the estimated costs. Use the following steps to review the cost estimate:

1. Sign into the Azure portal and navigate to your Azure Cosmos account.
1. Go to the **Overview** section.
1. Check the **Cost** chart at the bottom. This chart shows an estimate of your current cost over a configurable time period:
1. Create a new container such as a graph container.
1. Input the throughput required for your workload such as 400 RU/s. After you input the throughput value, you can see the pricing estimate as shown in the following screenshot:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Cost estimate in Azure portal":::

If your Azure subscription has a spending limit, Azure prevents you from spending over your credit amount. As you create and use Azure resources, your credits are used. When you reach your credit limit, the resources that you deployed are disabled for the rest of that billing period. You can't change your credit limit, but you can remove it. For more information about spending limits, see [Azure spending limit](../cost-management-billing/manage/spending-limit.md).

You can pay for Azure Cosmos DB charges with your Azure Enterprise Agreement monetary commitment credit. However, you can't use the monetary commitment credit to pay for charges for third party products and services including those from the Azure Marketplace.

## Monitor costs

As you use resources with Azure Cosmos DB, you incur costs. Resource usage unit costs vary by time intervals (seconds, minutes, hours, and days) or by request unit usage. As soon as usage of Azure Cosmos DB starts, costs are incurred and you can see them in the [cost analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pane in the Azure portal.

When you use cost analysis, you can view the Azure Cosmos DB costs in graphs and tables for different time intervals. Some examples are by day, current, prior month, and year. You can also view costs against budgets and forecasted costs. Switching to longer views over time can help you identify spending trends and see where overspending might have occurred. If you’ve created budgets, you can also easily see where they exceeded. 

To view Azure Cosmos DB costs in cost analysis:

1. Sign into the [Azure portal](https://portal.azure.com).

1. Open the scope in the Azure portal and select **Cost analysis** in the menu. For example, go to **Subscriptions**, select a subscription from the list, and then select  **Cost analysis** in the menu. Select **Scope** to switch to a different scope in cost analysis.

1. By default, cost for all services are shown in the first donut chart. Select the area in the chart labeled "Azure Cosmos DB".

1. To narrow costs for a single service such as Azure Cosmos DB, select **Add filter** and then select **Service name**. Then, choose **Azure Cosmos DB** from the list. Here’s an example showing costs for just Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Monitor costs with Cost Analysis pane":::

In the preceding example, you see the current cost for Azure Cosmos DB for the month of Feb. The charts also contain Azure Cosmos DB costs by location and by resource group.

## Create budgets

You can create [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) to manage costs and create [alerts](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) that automatically notify stakeholders of spending anomalies and overspending risks. Alerts are based on spending compared to budget and cost thresholds. Budgets and alerts are created for Azure subscriptions and resource groups, so they're useful as part of an overall cost monitoring strategy. 

Budgets can be created with filters for specific resources or services in Azure if you want more granularity present in your monitoring. Filters help ensure that you don't accidentally create new resources that cost you additional money. For more about the filter options when you when create a budget, see [Group and filter options](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## Export cost data

You can also [export your cost data](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) to a storage account. This is helpful when you need or others to do additional data analysis for costs. For example, a finance teams can analyze the data using Excel or Power BI. You can export your costs on a daily, weekly, or monthly schedule and set a custom date range. Exporting cost data is the recommended way to retrieve cost datasets.

## Next steps

See the following articles to learn more on how pricing works in Azure Cosmos DB:

* [Pricing model in Azure Cosmos DB](how-pricing-works.md)
* [Optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimize query cost in Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optimize storage cost in Azure Cosmos DB](optimize-cost-storage.md)
* Learn [how to optimize your cloud investment with Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Learn more about managing costs with [cost analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Learn about how to [prevent unexpected costs](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Take the [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guided learning course.