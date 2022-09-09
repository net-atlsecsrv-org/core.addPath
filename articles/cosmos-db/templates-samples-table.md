---
title: Resource Manager templates for Azure Cosmos DB Table API
description: Use Azure Resource Manager templates to create and configure Azure Cosmos DB Table API. 
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
---

# Manage Azure Cosmos DB Table API resources using Azure Resource Manager templates
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In this article, you learn how to use Azure Resource Manager templates to help deploy and manage your Azure Cosmos DB accounts, databases, and containers.

This article has examples for Table API accounts only, to find examples for other API type accounts see: use Azure Resource Manager templates with Azure Cosmos DB's API for [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDB](./templates-samples-mongodb.md), [SQL](./manage-with-templates.md) articles.

> [!IMPORTANT]
>
> * Account names are limited to 44 characters, all lowercase.
> * To change the throughput values, redeploy the template with updated RU/s.
> * When you add or remove locations to an Azure Cosmos account, you can't simultaneously modify other properties. These operations must be done separately.

To create any of the Azure Cosmos DB resources below, copy the following example template into a new json file. You can optionally create a parameters json file to use when deploying multiple instances of the same resource with different names and values. There are many ways to deploy Azure Resource Manager templates including, [Azure portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) and [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> To enable shared throughput when using Table API, enable account-level throughput in the Azure portal.

<a id="create-autoscale"></a>

## Azure Cosmos account for Table with autoscale throughput

This template will create an Azure Cosmos account for Table API with one table with autoscale throughput. This template is also available for one-click deploy from Azure Quickstart Templates Gallery.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Deploy to Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## Azure Cosmos account for Table with standard provisioned throughput

This template will create an Azure Cosmos account for Table API with one table with standard throughput. This template is also available for one-click deploy from Azure Quickstart Templates Gallery.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Deploy to Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## Next steps

Here are some additional resources:

* [Azure Resource Manager documentation](../azure-resource-manager/index.yml)
* [Azure Cosmos DB resource provider schema](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Quickstart templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Troubleshoot common Azure Resource Manager deployment errors](../azure-resource-manager/templates/common-deployment-errors.md)