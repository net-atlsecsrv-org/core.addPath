---
title: "Tutorial: Publish Azure Static Web Apps using an ARM template"
description: Create and deploy an ARM Template for Static Web Apps
services: static-web-apps
author: petender
ms.service: static-web-apps
ms.topic:  tutorial
ms.date: 04/18/2021
ms.author: petender

---

# Tutorial: Publish Azure Static Web Apps using an ARM Template

This article demonstrates how to deploy [Azure Static Web Apps](./overview.md) using an [Azure Resource Manager template](../azure-resource-manager/templates/overview.md) (ARM template).

In this tutorial, you learn to:

> [!div class="checklist"]
> - Create an ARM Template for Azure Static Web Apps
> - Deploy the ARM Template to create an Azure Static Web App instance

## Prerequisites

- **Active Azure account:** If you don't have one, you can [create an account for free](https://azure.microsoft.com/free/).
- **GitHub Account:** If you don't have one, you can [create a GitHub Account for free](https://github.com) 
- **Editor for ARM Templates:** Reviewing and editing templates requires a JSON editor. Visual Studio Code with the [Azure Resource Manager Tools extension](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) is well suited for editing ARM Templates. For instructions on how to install and configure Visual Studio Code, see [Quickstart: Create ARM templates with Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md).

- **Azure CLI or Azure PowerShell**: Deploying ARM templates requires a command line tool. For the installation instructions, see:
  - [Install Azure CLI on Windows OS](https://docs.microsoft.com/cli/azure/install-azure-cli-windows)
  - [Install Azure CLI on Linux OS](https://docs.microsoft.com/cli/azure/install-azure-cli-linux)
  - [Install Azure CLI on macOS](https://docs.microsoft.com/cli/azure/install-azure-cli-macos)
  - [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)


## Create a GitHub personal access token

One of the required parameters in the ARM template is `repositoryToken`, which allows the ARM deployment process to interact with the GitHub repo holding the static site source code. 

1. From your GitHub Account Profile (in the upper right corner), select **Settings**.

1. Select **Developer Settings**.

1. Select **Personal Access Tokens**.

1. Select **Generate New Token**.

1. Provide a name for this token in the _Note_ field, for example *myfirstswadeployment*.

1. Specify the following *scopes*: **repo, workflow, write:packages**

1. Select **Generate token**.

1. Copy the token value and paste it into a text editor for later use.

> [!IMPORTANT]
> Make sure you copy this token and store it somewhere safe. Consider storing this token in [Azure KeyVault](../azure-resource-manager/templates/template-tutorial-use-key-vault.md) and access it in your ARM Template. 
## Create a GitHub repo

The following steps demonstrate how to create a new repository for a static web app.

> [!NOTE]
> If you want to use an existing code repository you can skip this section.

1. Log on to [GitHub](https://github.com) using your GitHub account credentials.

1. Create a new repository named **myfirstswadeployment**.

1. Define your GitHub repo as **Public**.

1. Select the checkbox next to **Add a Readme file**.

1. Select **Create repository**.

1. Once the repository is created, select **Add file**.

1. Select **Create New file** and provide **index.html** as file name.

1. Paste the following snippet of code in the **Edit new file** pane

    ```html
    <!doctype html>
    <html>
      <head>
        <title>Hello World!</title>
      </head>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```
    
1. Scroll down and select **Commit new file** to save the file.

## Create the ARM Template

With the prerequisites in place, the next step is to define the ARM deployment template file.

1. Create a new folder to hold the ARM Templates.

1. Create a new file and name it **azuredeploy.json**.

1. Paste the following ARM template snippet into _azuredeploy.json_.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "type": "string"
                },
                "location": {
                    "type": "string"
                },
                "sku": {
                    "type": "string"
                },
                "skucode": {
                    "type": "string"
                },
                "repositoryUrl": {
                    "type": "string"
                },
                "branch": {
                    "type": "string"
                },
                "repositoryToken": {
                    "type": "securestring"
                },
                "appLocation": {
                    "type": "string"
                },
                "apiLocation": {
                    "type": "string"
                },
                "appArtifactLocation": {
                    "type": "string"
                },
                "resourceTags": {
                    "type": "object"
                }
            },
            "resources": [
                {
                    "apiVersion": "2019-12-01-preview",
                    "name": "[parameters('name')]",
                    "type": "Microsoft.Web/staticSites",
                    "location": "[parameters('location')]",
                    "tags": "[parameters('resourceTags')]",
                    "properties": {
                        "repositoryUrl": "[parameters('repositoryUrl')]",
                        "branch": "[parameters('branch')]",
                        "repositoryToken": "[parameters('repositoryToken')]",
                        "buildProperties": {
                            "appLocation": "[parameters('appLocation')]",
                            "apiLocation": "[parameters('apiLocation')]",
                            "appArtifactLocation": "[parameters('appArtifactLocation')]"
                        }
                    },
                    "sku": {
                        "Tier": "[parameters('sku')]",
                        "Name": "[parameters('skuCode')]"
                    }
                }
            ]
        }

    ```

1. Create a new file and name it **azuredeploy.parameters.json**.

1. Paste the following ARM template snippet into _azuredeploy.parameters.json_.

    ```json
        {
            "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "name": {
                    "value": "myfirstswadeployment"
                },
                "location": {
                "type": "string",
                "defaultValue": "Central US"
                },   
                "sku": {
                    "value": "Free"
                },
                "skucode": {
                    "value": "Free"
                },
                "repositoryUrl": {
                    "value": "https://github.com/<YOUR-GITHUB-USER-NAME>/<YOUR-GITHUB-REPOSITORY-NAME>"
                },
                "branch": {
                    "value": "main"
                },
                "repositoryToken": {
                    "value": "<YOUR-GITHUB-PAT>" 
                },
                "appLocation": {
                    "value": "/"
                },
                "apiLocation": {
                    "value": ""
                },
                "appArtifactLocation": {
                    "value": "public"
                },
                "resourceTags": {
                    "value": {
                        "Environment": "Development",
                        "Project": "Testing SWA with ARM",
                        "ApplicationName": "myfirstswadeployment"
                    }
                }
            }
        }
    ```

1. Update the following parameters.

    | Parameter | Expected value |
    | --- | --- |
    | `repositoryUrl` | Provide the URL to your Static Web Apps GitHub repository. |
    | `repositoryToken` | Provide the GitHub PAT token. |

1. Save the updates before running the deployment in the next step.

## Running the deployment

You need either Azure CLI or Azure PowerShell to deploy the template.

### Sign in to Azure

To deploy a template sign in to either the Azure CLI or Azure PowerShell.

# [PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

If you have multiple Azure subscriptions, select the subscription you want to use. Replace `<SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>` with your subscription information:

# [PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

# [Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription <SUBSCRIPTION-ID-OR-SUBSCRIPTION-NAME>
```

---

## Create a resource group

When you deploy a template, you specify a resource group that contains related resources. Before running the deployment command, create the resource group with either Azure CLI or Azure PowerShell.

> [!NOTE]
> The CLI examples in this article are written for the Bash shell.

# [PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = "myfirstswadeployRG"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# [Azure CLI](#tab/azure-cli)

```azurecli
resourceGroupName="myfirstswadeployRG"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## Deploy template

Use one of these deployment options to deploy the template.

# [PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$templateparameterFile = Read-Host -Prompt "Enter the template parameter file path and file name"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -TemplateParameterFile $templateparameterfile `
  -verbose
```

To learn more about deploying templates using Azure PowerShell, see [Deploy resources with ARM templates and Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

# [Azure CLI](#tab/azure-cli)

```azurecli

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file <PATH-TO-AZUREDEPLOY.JSON> \
  --parameters <PATH-TO-AZUREDEPLOY.PARAMETERS.JSON> \
  --verbose
```

To learn more about deploying templates using the Azure CLI, see [Deploy resources with ARM templates and Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

---
[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]
## Clean up resources

Clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name.
4. Select **Delete resource group** from the top menu.

## Next steps

> [!div class="nextstepaction"]
> [Configure your static web app](./configuration.md)
