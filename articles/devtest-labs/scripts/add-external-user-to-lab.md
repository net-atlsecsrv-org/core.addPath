---
title: PowerShell - Add an external user to a lab in Azure DevTest Labs
description: This article provides an Azure PowerShell script that adds an external user to a lab in Azure DevTest Labs.  
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
---

# Use PowerShell to add an external user to a lab in Azure DevTest Labs

This sample PowerShell script adds an external user to a lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## Prerequisites
* **A lab**. The script requires you to have an existing lab. 

## Sample script

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## Script explanation

This script uses the following commands: 

| Command | Notes |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Retries the user object from Azure active directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Assigns the specified role to the specified principal, at the specified scope. |

## Next steps

For more information on the Azure PowerShell, see [Azure PowerShell documentation](/powershell/).

Additional Azure Lab Services PowerShell script samples can be found in the [Azure Lab Services PowerShell samples](../samples-powershell.md).
