---
title: 'Create an App Service app using an Azure Resource Manager template'
description: Create your first app to Azure App Service in seconds using an Azure Resource Manager Template, which is one of many ways to deploy to App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
---
# Create App Service app using an Azure Resource Manager template

Get started with [Azure App Service](overview.md) by deploying a app to the cloud using an Azure Resource Manager template and [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) in Cloud Shell. Because you use a free App Service tier, you incur no costs to complete this quickstart.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## Prerequisites

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create an Azure App Service app

### Review the template

::: zone pivot="platform-windows"
The template used in this quickstart is from [Azure Quickstart templates](https://github.com/Azure/azure-quickstart-templates/). It deploys an App Service plan and an App Service app on Windows. It's compatible with .NET Core, .NET Framework, PHP, Node.js, and Static HTML apps. For Java, see [Create Java app](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Two Azure resources are defined in the template:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): create an App Service plan.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): create an App Service app.

This template contains several parameters that are predefined for your convenience. See the table below for parameter defaults and their descriptions:

| Parameters | Type    | Default value                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp-**[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)**" | App name |
| location   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | App region |
| sku        | string  | "F1"                         | Instance size (F1 = Free Tier) |
| language   | string  | ".net"                       | Programming language stack (.net, php, node, html) |
| helloWorld | boolean | False                        | True = Deploy "Hello World" app |
| repoUrl    | string  | " "                          | External Git repo (optional) |
::: zone-end
::: zone pivot="platform-linux"
The template used in this quickstart is from [Azure Quickstart templates](https://github.com/Azure/azure-quickstart-templates/). It deploys an App Service plan and an App Service app on Linux. It's compatible with all supported programming languages on App Service.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Two Azure resources are defined in the template:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): create an App Service plan.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): create an App Service app.

This template contains several parameters that are predefined for your convenience. See the table below for parameter defaults and their descriptions:

| Parameters | Type    | Default value                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp-**[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)**" | App name |
| location   | string  | "[[resourceGroup().location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | App region |
| sku        | string  | "F1"                         | Instance size (F1 = Free Tier) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | "Programming language stack &#124; Version" |
| repoUrl    | string  | " "                          | External Git repo (optional) |

---
::: zone-end


### Deploy the template

Azure CLI is used here to deploy the template. You can also use the Azure portal, Azure PowerShell, and REST API. To learn other deployment methods, see [Deploy templates](../azure-resource-manager/templates/deploy-powershell.md). 

The following code creates a resource group, an App Service plan, and a web app. A default resource group, App Service plan, and location have been set for you. Replace `<app-name>` with a globally unique app name (valid characters are `a-z`, `0-9`, and `-`).

::: zone pivot="platform-windows"
Run the code below to deploy a .NET framework app on Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

To deploy a different language stack, update `linuxFxVersion` with appropriate values. Samples are shown below. To show current versions, run the following command in the Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Language    | Example                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> You can find more [Azure App Service template samples here](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).


## Validate the deployment

Browse to `http://<app_name>.azurewebsites.net/` and verify it's been created.

## Clean up resources

When no longer needed, [delete the resource group](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## Next steps

> [!div class="nextstepaction"]
> [Deploy from local Git](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python with Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP with MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Connect to Azure SQL database with Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Map custom domain](app-service-web-tutorial-custom-domain.md)