---
title: Create an Azure portal dashboard with PowerShell
description: Learn how to create a dashboard in the Azure portal using Azure PowerShell.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
---

# Quickstart: Create an Azure portal dashboard with PowerShell

A dashboard in the Azure portal is a focused and organized view of your cloud resources. This
article focuses on the process of using the Az.Portal PowerShell module to create a dashboard.
The dashboard shows the performance of a virtual machine (VM), as well as some static information
and links.

## Requirements

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account
before you begin.

If you choose to use PowerShell locally, this article requires that you install the Az PowerShell
module and connect to your Azure account using the
[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
cmdlet. For more information about installing the Az PowerShell module, see
[Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> While the **Az.Portal** PowerShell module is in preview, you must install it separately from from
> the Az PowerShell module using the `Install-Module` cmdlet. Once this PowerShell module becomes
> generally available, it becomes part of future Az PowerShell module releases and available
> natively from within Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## Choose a specific Azure subscription

If you have multiple Azure subscriptions, choose the appropriate subscription in which the resources
should be billed. Select a specific subscription using the
[Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## Define variables

You'll be using several pieces of information repeatedly. Create variables to store the information.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## Create a resource group

Create an [Azure resource group](../azure-resource-manager/management/overview.md)
using the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
cmdlet. A resource group is a logical container in which Azure resources are deployed and managed as
a group.

The following example creates a resource group based on the name in the `$resourceGroupName`
variable in the region specified in the `$location` variable.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## Create a virtual machine

The dashboard you create in the next part of this quickstart requires an existing VM. Create a VM by
following these steps.

Store login credentials for the VM in a variable. The password must be complex. This is a new user
name and password; it's not, for example, the account you use to sign in to Azure. For more
information, see [username requirements](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)
and [password requirements](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Create the VM.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

The VM deployment now starts and typically takes a few minutes to complete. After deployment
completes, move on to the next section.

## Download the dashboard template

Since Azure dashboards are resources, they can be represented as JSON. The following code downloads
a JSON representation of a sample dashboard. For more information, see [The structure of Azure Dashboards](./azure-portal-dashboards-structure.md).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## Customize the template

Customize the downloaded template by running the following code.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

For more information, see [Microsoft portal dashboards template reference](/azure/templates/microsoft.portal/dashboards).

## Deploy the dashboard template

You can use the `New-AzPortalDashboard` cmdlet that's part of the Az.Portal module to deploy the
template directly from PowerShell.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## Review the deployed resources

Check that the dashboard was created successfully.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Verify that you can see data about the VM from within the Azure portal.

1. In the Azure portal, select **Dashboard**.

   ![Azure portal navigation to dashboard](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. On the dashboard page, select **Simple VM Dashboard**.

   ![Navigate to Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Review the dashboard. You can see that some of the content is static, but there are also charts
   that show the performance of the VM.

   ![Review Simple VM Dashboard](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## Clean up resources

To remove the VM and associated dashboard, delete the resource group that contains them.

> [!CAUTION]
> The following example deletes the specified resource group and all resources contained within it.
> If resources outside the scope of this article exist in the specified resource group, they will
> also be deleted.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## Next steps

For more information about the cmdlets contained in the Az.Portal PowerShell module, see:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: Portal Dashboard cmdlets](/powershell/module/Az.Portal/)