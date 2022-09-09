---
title: Plan and manage costs for Azure Blob storage
description: Learn how to plan for and manage costs for Azure Blob storage by using cost analysis in Azure portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
---

# Plan and manage costs for Azure Blob storage

This article helps you plan and manage costs for Azure Blob storage. First, estimate costs by using the Azure pricing calculator. After you create your storage account, optimize the account so that you pay only for what you need. Use cost management features to set budgets and monitor costs. You can also review forecasted costs, and monitor spending trends to identify areas where you might want to act.

Keep in mind that costs for Blob storage are only a portion of the monthly costs in your Azure bill. Although this article explains how to estimate and manage costs for Blob storage, you're billed for all Azure services and resources used for your Azure subscription, including the third-party services. After you're familiar with managing costs for Blob storage, you can apply similar methods to manage costs for all the Azure services used in your subscription.

## Estimate costs

Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate costs before you create and begin transferring data to an Azure Storage account.

1. On the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) page, choose the **Storage Accounts** tile.

2. Scroll down the page and locate the **Storage Accounts** section of your estimate.

3. Choose options from the drop-down lists. 

   As you modify the value of these drop-down lists, the cost estimate changes. That estimate appears in the upper corner as well as the bottom of the estimate.

   ![Screenshot showing your estimate](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   As you change the value of the **Type** drop-down list, other options that appear on this worksheet change as well. Use the links in the **More Info** section to learn more about what each option means and how these options affect the price of storage-related operations. 

4. Modify the remaining options to see their affect on your estimate.

## Optimize costs

Consider using these options to reduce costs. 

- Reserve storage capacity

- Organize data into access tiers

- Automatically move data between access tiers

This section covers each option in more detail. 

#### Reserve storage capacity

You can save money on storage costs for blob data with Azure Storage reserved capacity. Azure Storage reserved capacity offers you a discount on capacity for block blobs and for Azure Data Lake Storage Gen2 data in standard storage accounts when you commit to a reservation for either one year or three years. A reservation provides a fixed amount of storage capacity for the term of the reservation. Azure Storage reserved capacity can significantly reduce your capacity costs for block blobs and Azure Data Lake Storage Gen2 data. 

To learn more, see [Optimize costs for Blob storage with reserved capacity](../blobs/storage-blob-reserved-capacity.md).

#### Organize data into access tiers

You can reduce costs by placing blob data into the most cost effective access tiers. Choose from three tiers that are designed to optimize your costs around data use. For example, the *hot* tier has a higher storage cost but lower access cost. Therefore, if you plan to access data frequently, the hot tier might be the most cost-efficient choice. If you plan to access data less frequently, the *cold* or *archive* tier might make the most sense because it raises the cost of accessing data while reducing the cost of storing data.    

To learn more, see [Azure Blob storage: hot, cool, and archive access tiers](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal).

#### Automatically move data between access tiers

Use lifecycle management policies to periodically move data between tiers to save the most money. These policies can move data to by using rules that you specify. For example, you might create a rule that moves blobs to the archive tier if that blob hasn't been modified in 90 days. By creating policies that adjust the access tier of your data, you can design the least expensive storage options for your needs.

To learn more, see [Manage the Azure Blob storage lifecycle](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

## Create budgets

You can create [budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) to manage costs and create alerts that automatically notify stakeholders of spending anomalies and overspending risks. Alerts are based on spending compared to budget and cost thresholds. Budgets and alerts are created for Azure subscriptions and resource groups, so they're useful as part of an overall cost monitoring strategy. However, they might have limited functionality to manage individual Azure service costs like the cost of Azure Storage because they are designed to track costs at a higher level.

## Monitor costs

As you use Azure resources with Azure Storage, you incur costs. Resource usage unit costs vary by time intervals (seconds, minutes, hours, and days) or by unit usage (bytes, megabytes, and so on.) Costs are incurred as soon as usage of Azure Storage starts. You can see the costs in the [cost analysis](../../cost-management-billing/costs/quick-acm-cost-analysis.md) pane in the Azure portal.

When you use cost analysis, you can view Azure Storage costs in graphs and tables for different time intervals. Some examples are by day, current and prior month, and year. You can also view costs against budgets and forecasted costs. Switching to longer views over time can help you identify spending trends and see where overspending might have occurred. If you've created budgets, you can also easily see where they exceeded.

>[!NOTE]
> Cost analysis supports different kinds of Azure account types. To view the full list of supported account types, see [Understand Cost Management data](../../cost-management-billing/costs/understand-cost-mgt-data.md). To view cost data, you need at least read access for your Azure account. For information about assigning access to Azure Cost Management data, see [Assign access to data](../../cost-management-billing/costs/assign-access-acm-data.md).

To view Azure Storage costs in cost analysis:

1. Sign into the [Azure portal](https://portal.azure.com).

2. Open the **Cost Management + Billing** window, select **Cost management** from the menu and then select **Cost analysis**. You can then change the scope for a specific subscription from the **Scope** dropdown.

   ![Screenshot showing scope](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. To view only costs for Azure Storage, select **Add filter** and then select **Service name**. Then, choose **storage** from the list. 

   Here's an example showing costs for just Azure Storage:

   ![Screenshot showing filter by storage](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

In the preceding example, you see the current cost for the service. Costs by Azure regions (locations) and by resource group also appear. 
You can add other filters as well (For example: a filter to see costs for specific storage accounts).

## Next steps

Learn more about managing costs with [cost analysis](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

See the following articles to learn more on how pricing works with Azure Storage:

- [Azure Storage Overview pricing](https://azure.microsoft.com/pricing/details/storage/)
- [Optimize costs for Blob storage with reserved capacity](../blobs/storage-blob-reserved-capacity.md)