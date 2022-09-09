---
title: Manage Azure resources by using Azure PowerShell | Microsoft Docs
description: Use Azure PowerShell and Azure Resource Manage to manage your resources. 
services: azure-resource-manager
documentationcenter: ''
author: mumian


ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao

---
# Manage Azure resources by using Azure PowerShell

Learn how to use Azure PowerShell with [Azure Resource Manager](resource-group-overview.md) to manage your Azure resources. For managing resource groups, see [Manage Azure resource groups by using Azure PowerShell](./manage-resource-groups-powershell.md).

Other articles about managing resources:

- [Manage Azure resources by using the Azure portal](./manage-resources-portal.md)
- [Manage Azure resources by using Azure CLI](./manage-resources-cli.md)

## Deploy resources to an existing resource group

You can deploy Azure resources directly by using Azure PowerShell, or deploy a Resource Manager template to create Azure resources.

### Deploy a resource

The following script creates a storage account.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### Deploy a template

The following script creates deploy a Quickstart template to create a storage account. For more information, see [Quickstart: Create Azure Resource Manager templates by using Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

For more information, see [Deploy resources with Resource Manager templates and Azure PowerShell](./resource-group-template-deploy.md).

## Deploy a resource group and resources

You can create a resource group and deploy resources to the group. For more information, see [Create resource group and deploy resources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## Deploy resources to multiple subscriptions or resource groups

Typically, you deploy all the resources in your template to a single resource group. However, there are scenarios where you want to deploy a set of resources together but place them in different resource groups or subscriptions. For more information, see [Deploy Azure resources to multiple subscriptions or resource groups](./resource-manager-cross-resource-group-deployment.md).

## Delete resources

The following script shows how to delete a storage account.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

For more information about how Azure Resource Manager orders the deletion of resources, see [Azure Resource Manager resource group deletion](./resource-group-delete.md).

## Move resources

The following script shows how to remove a storage account from one resource group to another resource group.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

For more information, see [Move resources to new resource group or subscription](resource-group-move-resources.md).

## Lock resources

Locking prevents other users in your organization from accidentally deleting or modifying critical resources, such as Azure subscription, resource group, or resource. 

The following script locks a storage account so the account can't be deleted.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

The following script gets all locks for a storage account:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

The following script deletes a lock of a storage account:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

For more information, see [Lock resources with Azure Resource Manager](resource-group-lock-resources.md).

## Tag resources

Tagging helps organizing your resource group and resources logically. For information, see [Using tags to organize your Azure resources](./resource-group-using-tags.md#powershell).

## Manage access to resources

[Role-based access control (RBAC)](../role-based-access-control/overview.md) is the way that you manage access to resources in Azure. For more information, see [Manage access using RBAC and Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## Next steps

- To learn Azure Resource Manager, see [Azure Resource Manager overview](./resource-group-overview.md).
- To learn the Resource Manager template syntax, see [Understand the structure and syntax of Azure Resource Manager templates](./resource-group-authoring-templates.md).
- To learn how to develop templates, see the [step-by-step tutorials](/azure/azure-resource-manager/).
- To view the Azure Resource Manager template schemas, see [template reference](/azure/templates/).
