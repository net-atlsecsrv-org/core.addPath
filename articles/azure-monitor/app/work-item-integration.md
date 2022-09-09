---
title: Work Item Integration (preview) - Application Insights
description: Learn how to create work items in GitHub or Azure DevOps with Application Insights data embedded in them.
ms.topic: conceptual
ms.date: 02/9/2021

---

# Work Item Integration (preview)

Work item integration functionality allows you to easily create work items in GitHub or Azure DevOps that have relevant Application Insights data embedded in them.

> [!IMPORTANT]
> Work Item integration is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Create and configure a work item template

1. To create a work item template, go to your Application Insights resource and on the left under *Configure* select **Work Items** then at the top select **Create a new template**

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" Screenshot of the Work Items tab with create a new template selected." lightbox="./media/work-item-integration/create-work-item-template.png":::

    You can also create a work item template from the End-to-end transaction details tab, if no template currently exists. Select an event and on the right select **Create a work item**, then **Start with a workbook template**.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" Screenshot of  end-to-end transaction details tab with create a work item, start with a workbook template selected." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. After you select **create a new template**, you can choose your tracking systems, name your workbook, link to your selected tracking system, and choose a region to storage the template (the default is the region your Application Insights resource is located in). The URL parameters are the default URL for your repository, for example, `https://github.com/myusername/reponame` or `https://mydevops.visualstudio.com/myproject`.

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" Screenshot of create a new work item workbook template.":::

## Create a work item

 You can access your new template from any End-to-end transaction details that you can access from Performance, Failures, Availability, or other tabs.

1. To create a work item go to End-to-end transaction details, select an event then select **Create work item** and choose your work item template.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" Screenshot of end to end transaction details tab with create work item selected." lightbox="./media/work-item-integration/create-work-item.png":::

1. A new tab in your browser will open up to your select tracking system. In Azure DevOps you can create a bug or task, and in GitHub you can create a new issue in your repository. A new work item is automatically create with contextual information provided by Application Insights.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" Screenshot of automatically created GitHub issue" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Screenshot of automatically create bug in Azure DevOps." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## Edit a template

To edit your template, go to the **Work Items** tab under *Configure* and select the pencil icon next to the workbook you would like to update.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" Screenshot of work item tab with the edit pencil icon selected.":::

Select edit ![edit icon](./media/work-item-integration/edit-icon.png) at the top to begin editing the template. Work Item templates are based on [Azure Monitor Workbooks](../visualize/workbooks-overview.md). The work item information is generated using the keyword query language. You can modify the queries to add more context essential to your team. When you are done editing, save the workbook by selecting the save icon ![save icon](./media/work-item-integration/save-icon.png) in the top toolbar.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" Screenshot of the work item template workbook in edit mode." lightbox="./media/work-item-integration/edit-workbook.png":::

You can create more than one work item configuration and have a custom workbook to meet each scenario. The workbooks can also be deployed by Azure Resource Manager ensuring standard implementations across your environments.