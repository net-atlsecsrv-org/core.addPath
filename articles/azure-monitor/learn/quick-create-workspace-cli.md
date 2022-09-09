---
title: Create a Log Analytics workspace using Azure CLI | Microsoft Docs
description: Learn how to create a Log Analytics workspace to enable management solutions and data collection from your cloud and on-premises environments with Azure CLI.
ms.service:  azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019

---

# Create a Log Analytics workspace with Azure CLI 2.0

The Azure CLI 2.0 is used to create and manage Azure resources from the command line or in scripts. This quickstart shows you how to use Azure CLI 2.0 to deploy a Log Analytics workspace in Azure Monitor. A Log Analytics workspace is a unique environment for Azure Monitor log data. Each workspace has its own data repository and configuration, and data sources and solutions are configured to store their data in a particular workspace. You require a Log Analytics workspace if you intend on collecting data from the following sources:

* Azure resources in your subscription  
* On-premises computers monitored by System Center Operations Manager  
* Device collections from System Center Configuration Manager  
* Diagnostic or log data from Azure storage  
 
For other sources, such as Azure VMs and Windows or Linux VMs in your environment, see the following topics:

* [Collect data from Azure virtual machines](../learn/quick-collect-azurevm.md)
* [Collect data from hybrid Linux computer](../learn/quick-collect-linux-computer.md)
* [Collect data from hybrid Windows computer](quick-collect-windows-computer.md)

If you don't have an Azure subscription, create [a free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this quickstart requires that you are running the Azure CLI version 2.0.30 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## Create a workspace
Create a workspace with [az group deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). The following example creates a workspace in the *eastus* location using a Resource Manager template from your local machine. The  JSON template is configured to only prompt you for the name of the workspace, and specifies a default value for the other parameters that would likely be used as a standard configuration in your environment. Or you can store the template in an Azure storage account for shared access in your organization. For further information about working with templates, see [Deploy resources with Resource Manager templates and Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

For information about regions supported, see [regions Log Analytics is available in](https://azure.microsoft.com/regions/services/) and search for Azure Monitor from the **Search for a product** field. 

The following parameters set a default value:

* location - defaults to East US
* sku - defaults to the new Per-GB pricing tier released in the April 2018 pricing model

>[!WARNING]
>If creating or configuring a Log Analytics workspace in a subscription that has opted into the new April 2018 pricing model, the only valid Log Analytics pricing tier is **PerGB2018**. 
>

### Create and deploy template

1. Copy and paste the following JSON syntax into your file:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
			"metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
			"allowedValues": [
			  "eastus",
			  "westus"
			],
			"defaultValue": "eastus",
			"metadata": {
			  "description": "Specifies the location in which to create the workspace."
			}
        },
        "sku": {
            "type": "String",
			"allowedValues": [
              "Standalone",
              "PerNode",
		      "PerGB2018"
            ],
			"defaultValue": "PerGB2018",
	        "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
		}
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edit the template to meet your requirements. Review [Microsoft.OperationalInsights/workspaces template](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) reference to learn what properties and values are supported. 
3. Save this file as **deploylaworkspacetemplate.json** to a local folder.   
4. You are ready to deploy this template. Use the following commands from the folder containing the template. When you're prompted for a workspace name, provide a name that is globally unique across all Azure subscriptions.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

The deployment can take a few minutes to complete. When it finishes, you see a message similar to the following that includes the result:

![Example result when deployment is complete](media/quick-create-workspace-cli/template-output-01.png)

## Next steps
Now that you have a workspace available, you can configure collection of monitoring telemetry, run log searches to analyze that data, and add a management solution to provide additional data and analytic insights.  

* To enable data collection from Azure resources with Azure Diagnostics or Azure storage, see [Collect Azure service logs and metrics for use in Log Analytics](../platform/collect-azure-metrics-logs.md).  
* Add [System Center Operations Manager as a data source](../platform/om-agents.md) to collect data from agents reporting your Operations Manager management group and store it in your Log Analytics workspace.  
* Connect [Configuration Manager](../platform/collect-sccm.md) to import computers that are members of collections in the hierarchy.  
* Review the [monitoring solutions](../insights/solutions.md) available and how to add or remove a solution from your workspace.
