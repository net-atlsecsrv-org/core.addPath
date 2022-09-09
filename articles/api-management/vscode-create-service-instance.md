---
title: Create an Azure API Management instance using Visual Studio Code | Microsoft Docs
description: Visual Studio Code to create an Azure API Management instance.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
---

# Quickstart: Create a new Azure API Management service instance using Visual Studio Code

Azure API Management (APIM) helps organizations publish APIs to external, partner, and internal developers to unlock the potential of their data and services. API Management provides the core competencies to ensure a successful API program through developer engagement, business insights, analytics, security, and protection. APIM  enables you to create and manage modern API gateways for existing backend services hosted anywhere. For more information, see the [Overview](api-management-key-concepts.md) topic.

This quickstart describes the steps for creating a new API Management instance using the *Azure API Management Extension Preview* for Visual Studio Code. You can also use the extension to perform common management operations on your API Management instance.

## Prerequisites

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Additionally, ensure you have installed the following:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure API Management Extension for Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## Sign in to Azure

Launch Visual Studio Code and open the Azure extension. (If you don't see the Azure icon on the Activity Bar, make sure the *Azure API Management* extension is enabled.)

Select **Sign in to Azure...** to launch a browser window and sign in to your Microsoft account.

![Sign in to Azure from the API Management extension for VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## Create an API Management service

Once you're signed in to your Microsoft account, the *Azure: API Management* explorer pane will list your Azure subscription(s).

Right-click on the subscription you'd like to use, and select **Create API Management in Azure**.

![Create API Management wizard in VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

In the pane that opens, supply a name for the new API Management instance. It must be globally unique within Azure and consist of 1-50 alphanumeric characters and/or hyphens, and start with a letter and end with an alphanumeric.

A new API Management instance (and parent resource group) will be created with the specified name. By default, the instance is created in the *West US* region with *Consumption* SKU.

> [!TIP]
> If you enable **Advanced Creation** in the *Azure API Management Extension Settings*, you can also specify an [API Management SKU](https://azure.microsoft.com/pricing/details/api-management/), [Azure region](https://status.azure.com/en-us/status), and a [resource group](../azure-resource-manager/management/overview.md) to deploy your API Management instance.
>
> While the *Consumption* SKU takes less than a minute to provision, other SKUs typically take 30-40 minutes to create.

At this point, you're ready to import and publish your first API. You can do that and also perform common API Management operations within the extension for Visual Studio Code. See the [API Management Extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) documentation for more.

![Newly created API Management instance in VS Code API Management extension pane](./media/vscode-create-service-instance/vscode-apim-instance.png)

## Clean up resources

When no longer needed, remove the API Management instance by right-clicking and selecting **Open in Portal** to [delete the API Management service](get-started-create-service-instance.md#clean-up-resources) and its resource group.

Alternately, you can select **Delete API Management** to only delete the API Management instance (this operation doesn't delete its resource group).

![Delete API Management instance from VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## Next steps

> [!div class="nextstepaction"]
> [Import and publish your first API](import-and-publish.md)
