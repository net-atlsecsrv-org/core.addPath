---
title: Cross service query between Azure Monitor and Azure Data Explorer (preview)
description: Query Azure Data Explorer data through Azure Log Analytics tools vice versa to join and analyze all your data in one place.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
---

# Cross service query - Azure Monitor and Azure Data Explorer (Preview)
Create cross service queries between [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/), [Application Insights](/azure/azure-monitor/app/app-insights-overview), and [Log Analytics](/azure/azure-monitor/platform/data-platform-logs).
## Azure Monitor and Azure Data Explorer cross-service querying
This experience enables you to [create cross service queries between Azure Data Explorer and Azure Monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) and to [create cross service queries between Azure Monitor and Azure Data Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy).

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure data explorer proxy flow.":::

For example, (querying Azure Data Explorer from Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Where the outer query is querying a table in the workspace, and then joining with another table in an Azure Data Explorer cluster (in this case, clustername=help, databasename=samples) by using a new "adx()" function, like how you can do the same to query another workspace from inside query text.

> [!NOTE]
> * The ability to query Azure Monitor data from Azure Data Explorer, either directly from Azure Data Explorer client tools, or indirectly by running a query on an Azure Data Explorer cluster, is in preview mode.
> * Contact the [Cross service query](mailto:adxproxy@microsoft.com) team with any questions.

## Query exported Log Analytics data from Azure Blob storage account

Exporting data from Azure Monitor to an Azure storage account enables low-cost retention and the ability to reallocate logs to different regions.

Use Azure Data Explorer to query data that was exported from your Log Analytics workspaces. Once configured, supported tables that are sent from your workspaces to an Azure storage account will be available as a data source for Azure Data Explorer. [Query exported data from Azure Monitor using Azure Data Explorer (preview)](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage).

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Azure Data Explorer query from storage flow.":::

>[!tip] 
> * To export all data from your Log Analytics workspace to an Azure storage account or event hub, use the Log Analytics workspace data export feature of Azure Monitor Logs. [See Log Analytics workspace data export in Azure Monitor (preview)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## Next steps
Learn more about:
* [create cross service queries between Azure Data Explorer and Azure Monitor](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Query Azure Monitor data from Azure Data Explorer
* [create cross service queries between Azure Monitor and Azure Data Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy). Query Azure Data Explorer data from Azure Monitor
* [Log Analytics workspace data export in Azure Monitor (preview)](https://docs.microsoft.com/azure/data-explorer/query-monitor-data). Link and query Azure Blob storage account with Log Analytics Exported data.