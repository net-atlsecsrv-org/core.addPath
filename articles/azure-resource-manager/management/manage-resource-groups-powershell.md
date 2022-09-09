---
title: Manage resource groups - Azure PowerShell
description: Use Azure PowerShell to manage your resource groups through Azure Resource Manager. Shows how to create, list, and delete resource groups.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao

---
# Manage Azure Resource Manager resource groups by using Azure PowerShell

Learn how to use Azure PowerShell with [Azure Resource Manager](overview.md) to manage your Azure resource groups. For managing Azure resources, see [Manage Azure resources by using Azure PowerShell](manage-resources-powershell.md).

Other articles about managing resource groups:

- [Manage Azure resource groups by using the Azure portal](manage-resources-portal.md)
- [Manage Azure resource groups by using Azure CLI](manage-resources-cli.md)

## What is a resource group

A resource group is a container that holds related resources for an Azure solution. The resource group can include all the resources for the solution, or only those resources that you want to manage as a group. You decide how you want to allocate resources to resource groups based on what makes the most sense for your organization. Generally, add resources that share the same lifecycle to the same resource group so you can easily deploy, update, and delete them as a group.

The resource group stores metadata about the resources. Therefore, when you specify a location for the resource group, you're specifying where that metadata is stored. For compliance reasons, you may need to ensure that your data is stored in a particular region.

The resource group stores metadata about the resources. When you specify a location for the resource group, you're specifying where that metadata is stored.

## Create resource groups

The following PowerShell script creates a resource group.

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## List resource groups

The following PowerShell script lists the resource groups under your subscription.

```azurepowershell-interactive
Get-AzResourceGroup
```

To get one resource group:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## Delete resource groups

The following PowerShell script deletes a resource group:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion](delete-resource-group.md).

## Deploy resources to an existing resource group

See [Deploy resources to an existing resource group](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

To validate a resource group deployment, see [Test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## Deploy a resource group and resources

You can create a resource group and deploy resources to the group by using a Resource Manager template. For more information, see [Create resource group and deploy resources](../templates/deploy-to-subscription.md#resource-groups).

## Redeploy when deployment fails

This feature is also known as *Rollback on error*. For more information, see [Redeploy when deployment fails](../templates/rollback-on-error.md).

## Move to another resource group or subscription

You can move the resources in the group to another resource group. For more information, see [Move resources to new resource group or subscription](move-resource-group-and-subscription.md).

## Lock resource groups

Locking prevents other users in your organization from accidentally deleting or modifying critical resources, such as Azure subscription, resource group, or resource. 

The following script locks a resource group so the resource group can't be deleted.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

The following script gets all locks for a resource group:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

For more information, see [Lock resources with Azure Resource Manager](lock-resources.md).

## Tag resource groups

You can apply tags to resource groups and resources to logically organize your assets. For information, see [Using tags to organize your Azure resources](tag-resources.md#powershell).

## Export resource groups to templates

After setting up your resource group, you can view a Resource Manager template for the resource group. Exporting the template offers two benefits:

- Automate future deployments of the solution because the template contains the complete infrastructure.
- Learn template syntax by looking at the JavaScript Object Notation (JSON) that represents your solution.

To export all resources in a resource group, use the [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet and provide the resource group name.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

It saves the template as a local file.

Instead of exporting all resources in the resource group, you can select which resources to export.

To export one resource, pass that resource ID.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

To export more than one resource, pass the resource IDs in an array.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

When exporting the template, you can specify whether parameters are used in the template. By default, parameters for resource names are included but they don't have a default value. You must pass that parameter value during deployment.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

In the resource, the parameter is used for the name.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

If you use the `-IncludeParameterDefaultValue` parameter when exporting the template, the template parameter includes a default value that is set to the current value. You can either use that default value or overwrite the default value by passing in a different value.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

If you use the `-SkipResourceNameParameterization` parameter when exporting the template, parameters for resource names aren't included in the template. Instead, the resource name is set directly on the resource to its current value. You can't customize the name during deployment.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

The export template feature doesn't support exporting Azure Data Factory resources. To learn about how you can export Data Factory resources, see [Copy or clone a data factory in Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

To export resources created through classic deployment model, you must [migrate them to the Resource Manager deployment model](https://aka.ms/migrateclassicresourcetoarm).

For more information, see [Single and multi-resource export to template in Azure portal](../templates/export-template-portal.md).

## Manage access to resource groups

[Azure role-based access control (Azure RBAC)](../../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. For more information, see [Manage access using RBAC and Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## Next steps

- To learn Azure Resource Manager, see [Azure Resource Manager overview](overview.md).
- To learn the Resource Manager template syntax, see [Understand the structure and syntax of Azure Resource Manager templates](../templates/template-syntax.md).
- To learn how to develop templates, see the [step-by-step tutorials](../index.yml).
- To view the Azure Resource Manager template schemas, see [template reference](/azure/templates/).
