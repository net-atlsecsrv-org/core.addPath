---
title: Create an Azure App Configuration store by using Azure Resource Manager template (ARM template)
titleSuffix: Azure App Configuration
description: Learn how to create an Azure App Configuration store by using Azure Resource Manager template (ARM template).
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 10/16/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
---

# Quickstart: Create an Azure App Configuration store by using an ARM template

This quickstart describes how to :

- Deploy an App Configuration store using an Azure Resource Manager template (ARM template).
- Create key-values in an App Configuration store using ARM template.
- Read key-values in an App Configuration store from ARM template.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Review the template

The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/). It creates a new App Configuration store with two key-values inside. It then uses the `reference` function to output the values of the two key-value resources. Reading the key's value in this way allows it to be used in other places in the template.

The quickstart uses the `copy` element to create multiple instances of key-value resource. To learn more about the `copy` element, see [Resource iteration in ARM templates](../azure-resource-manager/templates/copy-resources.md).

> [!IMPORTANT]
> This template requires App Configuration resource provider version `2020-07-01-preview` or later. This version uses the `reference` function to read key-values. The `listKeyValue` function that was used to read key-values in the previous version is not available starting in version `2020-07-01-preview`.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

Two Azure resources are defined in the template:

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): create an App Configuration store.
- Microsoft.AppConfiguration/configurationStores/keyValues: create a key-value inside the App Configuration store.

> [!NOTE]
> The `keyValues` resource's name is a combination of key and label. The key and label are joined by the `$` delimiter. The label is optional. In the above example, the `keyValues` resource with name `myKey` creates a key-value without a label.
>
> Percent-encoding, also known as URL encoding, allows keys or labels to include characters that are not allowed in ARM template resource names. `%` is not an allowed character either, so `~` is used in its place. To correctly encode a name, follow these steps:
>
> 1. Apply URL encoding
> 2. Replace `~` with `~7E`
> 3. Replace `%` with `~`
>
> For example, to create a key-value pair with key name `AppName:DbEndpoint` and label name `Test`, the resource name should be `AppName~3ADbEndpoint$Test`.

## Deploy the template

Select the following image to sign in to Azure and open a template. The template creates an App Configuration store with two key-values inside.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

You can also deploy the template by using the following PowerShell cmdlet. The key-values will be in the output of PowerShell console.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## Review deployed resources

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In the Azure portal search box, type **App Configuration**. Select **App Configuration** from the list.
1. Select the newly created App Configuration resource.
1. Under **Operations**, click **Configuration explorer**.
1. Verify that two key-values exist.

## Clean up resources

When no longer needed, delete the resource group, the App Configuration store, and all related resources. If you're planning to use the App Configuration store in the future, you can skip deleting it. If you aren't going to continue to use this store, delete all resources created by this quickstart by running the following cmdlet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## Next steps

To learn about creating other applications with Azure App Configuration, continue to the following article:

> [!div class="nextstepaction"]
> [Quickstart: Create an ASP.NET Core app with Azure App Configuration](quickstart-aspnet-core-app.md)
