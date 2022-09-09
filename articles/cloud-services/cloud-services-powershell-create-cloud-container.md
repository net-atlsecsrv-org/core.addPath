---
title: Create a cloud service (classic) container with PowerShell | Microsoft Docs
description: This article explains how to create a cloud service container with PowerShell. The container hosts web and worker roles.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: 
---

# Use an Azure PowerShell command to create an empty cloud service (classic) container

> [!IMPORTANT]
> [Azure Cloud Services (extended support)](../cloud-services-extended-support/overview.md) is a new Azure Resource Manager based deployment model for the Azure Cloud Services product. With this change, Azure Cloud Services running on the Azure Service Manager based deployment model have been renamed as Cloud Services (classic) and all new deployments should use [Cloud Services (extended support)](../cloud-services-extended-support/overview.md).

This article explains how to quickly create a Cloud Services container using Azure PowerShell cmdlets. Please follow the steps below:

1. Install the Microsoft Azure PowerShell cmdlet from the [Azure PowerShell downloads](https://aka.ms/webpi-azps) page.
2. Open the PowerShell command prompt.
3. Use the [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) to sign in.

   > [!NOTE]
   > For further instruction on installing the Azure PowerShell cmdlet and connecting to your Azure subscription, refer to [How to install and configure Azure PowerShell](/powershell/azure/).
   >
   >
4. Use the **New-AzureService** cmdlet to create an empty Azure cloud service container.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Follow this example to invoke the cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

For more information about creating the Azure cloud service, run:

```powershell
Get-help New-AzureService
```

### Next steps

* To manage the cloud service deployment, refer to the [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService), and [Set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) commands. You may also refer to [How to configure cloud services](cloud-services-how-to-configure-portal.md) for further information.
* To publish your cloud service project to Azure, refer to the  **PublishCloudService.ps1** code sample from [archived cloud services repository](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).