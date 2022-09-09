---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
---
## Sign in to Azure

Sign in to your Azure subscription with the `Connect-AzAccount` command and follow the on-screen directions.

```powershell
Connect-AzAccount
```

If you don't know which location you want to use, you can list the available locations. Display the list of locations by using the following code example and find the one you want to use. This example uses **eastus**. Store the location in a variable and use the variable so you can change it in one place.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## Create a resource group

Create an Azure resource group with [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A resource group is a logical container into which Azure resources are deployed and managed.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## Create a storage account

Create a standard, general-purpose storage account with LRS replication by using [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Next, get the storage account context that defines the storage account you want to use. When acting on a storage account, reference the context instead of repeatedly passing in the credentials. Use the following example to create a storage account called *mystorageaccount* with locally redundant storage (LRS) and blob encryption (enabled by default).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
