---
title: Plan to manage costs for Azure Cognitive Services
description: Learn how to plan for and manage costs for Azure Cognitive Services by using cost analysis in the Azure portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
---


# Plan and manage costs for Azure Cognitive Services

This article describes how you plan for and manage costs for Azure Cognitive Services. First, you use the Azure pricing calculator to help plan for Cognitive Services costs before you add any resources for the service to estimate costs. Next, as you add Azure resources, review the estimated costs. After you've started using Cognitive Services resources (for example Speech, Computer Vision, LUIS, Text Analytics, Translator, etc.), use Cost Management features to set budgets and monitor costs. You can also review forecasted costs and identify spending trends to identify areas where you might want to act. Costs for Cognitive Services are only a portion of the monthly costs in your Azure bill. Although this article explains how to plan for and manage costs for Cognitive Services, you're billed for all Azure services and resources used in your Azure subscription, including the third-party services.

## Prerequisites

Cost analysis in Cost Management supports most Azure account types, but not all of them. To view the full list of supported account types, see [Understand Cost Management data](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). To view cost data, you need at least read access for an Azure account. For information about assigning access to Azure Cost Management data, see [Assign access to data](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## Estimate costs before using Cognitive Services

Use the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) to estimate costs before you add Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Azure Pricing calculator for Cognitive Services" border="true":::

As you add new resources to your workspace, return to this calculator and add the same resource here to update your cost estimates.

For more information, see [Azure Cognitive Services pricing](https://azure.microsoft.com/pricing/details/cognitive-services/).

## Understand the full billing model for Cognitive Services

Cognitive Services runs on Azure infrastructure that [accrues costs](https://azure.microsoft.com/pricing/details/cognitive-services/) when you deploy the new resource. It's important to understand that additional infrastructure might accrue cost. You need to manage that cost when you make changes to deployed resources. 

### Costs that typically accrue with Cognitive Services

Typically, after you deploy an Azure resource, costs are determined by your pricing tier and the API calls you make to your endpoint. If the service you're using has a commitment tier, going over the allotted calls in your tier may incur an overage charge.

Additional costs may accrue when using these services:

#### QnA Maker

When you create resources for QnA Maker, resources for other Azure services may also be created. They include:

- [Azure App Service (for the runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (for the data)](https://azure.microsoft.com/pricing/details/search/)
 
### Costs that might accrue after resource deletion

#### QnA Maker

After you delete QnA Maker resources, the following resources might continue to exist. They continue to accrue costs until you delete them.

- [Azure App Service (for the runtime)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (for the data)](https://azure.microsoft.com/pricing/details/search/)

### Using Monetary Credit with Cognitive Services

You can pay for Cognitive Services charges with your EA monetary commitment credit. However, you can't use EA monetary commitment credit to pay for charges for third party products and services including those from the Azure Marketplace.

## Create budgets

You can create [budgets](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) to manage costs and create [alerts](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) that automatically notify stakeholders of spending anomalies and overspending risks. Alerts are based on spending compared to budget and cost thresholds. Budgets and alerts are created for Azure subscriptions and resource groups, so they're useful as part of an overall cost monitoring strategy. 

Budgets can be created with filters for specific resources or services in Azure if you want more granularity present in your monitoring. Filters help ensure that you don't accidentally create new resources that cost you additional money. For more about the filter options when you when create a budget, see [Group and filter options](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## Export cost data

You can also [export your cost data](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) to a storage account. This is helpful when you need or others to do additional data analysis for costs. For example, finance teams can analyze the data using Excel or Power BI. You can export your costs on a daily, weekly, or monthly schedule and set a custom date range. Exporting cost data is the recommended way to retrieve cost datasets.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## Next steps

- Learn [how to optimize your cloud investment with Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Learn more about managing costs with [cost analysis](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Learn about how to [prevent unexpected costs](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Take the [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) guided learning course.
