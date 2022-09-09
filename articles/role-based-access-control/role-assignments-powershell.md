---
title: Add or remove Azure role assignments using Azure PowerShell - Azure RBAC
description: Learn how to grant access to Azure resources for users, groups, service principals, or managed identities using Azure PowerShell and Azure role-based access control (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
---

# Add or remove Azure role assignments using Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] This article describes how to assign roles using Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## Prerequisites

To add or remove role assignments, you must have:

- `Microsoft.Authorization/roleAssignments/write` and `Microsoft.Authorization/roleAssignments/delete` permissions, such as [User Access Administrator](built-in-roles.md#user-access-administrator) or [Owner](built-in-roles.md#owner)
- [PowerShell in Azure Cloud Shell](../cloud-shell/overview.md) or [Azure PowerShell](/powershell/azure/install-az-ps)

## Steps to add a role assignment

In Azure RBAC, to grant access, you add a role assignment. A role assignment consists of three elements: security principal, role definition, and scope. To add a role assignment, follow these steps.

### Step 1: Determine who needs access

You can assign a role to a user, group, service principal, or managed identity. To add a role assignment, you might need to specify the unique ID of the object. The ID has the format: `11111111-1111-1111-1111-111111111111`. You can get the ID using the Azure portal or Azure PowerShell.

**User**

For an Azure AD user, get the user principal name, such as *patlong\@contoso.com* or the user object ID. To get the object ID, you can use [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Group**

For an Azure AD group, you need the group object ID. To get the object ID, you can use [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Service principal**

For an Azure AD service principal (identity used by an application), you need the service principal object ID. To get the object ID, you can use [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). For a service principal, use the object ID and **not** the application ID.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Managed identity**

For a system-assigned or a user-assigned managed identity, you need the object ID. To get the object ID, you can use [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### Step 2: Find the appropriate role

Permissions are grouped together into roles. You can select from a list of several [Azure built-in roles](built-in-roles.md) or you can use your own custom roles. It's a best practice to grant access with the least privilege that is needed, so avoid assigning a broader role.

To list roles and get the unique role ID, you can use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Here's how to list the details of a particular role.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

For more information, see [List Azure role definitions](role-definitions-list.md#azure-powershell).
 
### Step 3: Identify the needed scope

Azure provides four levels of scope: resource, [resource group](../azure-resource-manager/management/overview.md#resource-groups), subscription, and [management group](../governance/management-groups/overview.md). It's a best practice to grant access with the least privilege that is needed, so avoid assigning a role at a broader scope. For more information about scope, see [Understand scope](scope-overview.md).

**Resource scope**

For resource scope, you need the resource ID for the resource. You can find the resource ID by looking at the properties of the resource in the Azure portal. A resource ID has the following format.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Resource group scope**

For resource group scope, you need the name of the resource group. You can find the name on the **Resource groups** page in the Azure portal or you can use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Subscription scope** 

For subscription scope, you need the subscription ID. You can find the ID on the **Subscriptions** page in the Azure portal or you can use [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Management group scope** 

For management group scope, you need the management group name. You can find the name on the **Management groups** page in the Azure portal or you can use [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### Step 4: Add role assignment

To add a role assignment, use the [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) command. Depending on the scope, the command typically has one of the following formats.

**Resource scope**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Resource group scope**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Subscription scope** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Management group scope** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## Add role assignment examples

#### Add role assignment for all blob containers in a storage account resource scope

Assigns the [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) role to a service principal with object ID *55555555-5555-5555-5555-555555555555* at a resource scope for a storage account named *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### Add role assignment for a specific blob container resource scope

Assigns the [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) role to a service principal with object ID *55555555-5555-5555-5555-555555555555* at a resource scope for a blob container named *blob-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### Add role assignment for a group in a specific virtual network resource scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to the *Pharma Sales Admins* group with ID aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa at a resource scope for a virtual network named *pharma-sales-project-network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### Add a role assignment for a user at a resource group scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to *patlong\@contoso.com* user at the *pharma-sales* resource group scope.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternately, you can specify the fully qualified resource group with the `-Scope` parameter:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### Add role assignment for a user using the unique role ID at a resource group scope

There are a couple of times when a role name might change, for example:

- You are using your own custom role and you decide to change the name.
- You are using a preview role that has **(Preview)** in the name. When the role is released, the role is renamed.

Even if a role is renamed, the role ID does not change. If you are using scripts or automation to create your role assignments, it's a best practice to use the unique role ID instead of the role name. Therefore, if a role is renamed, your scripts are more likely to work.

The following example assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to the *patlong\@contoso.com* user at the *pharma-sales* resource group scope.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### Add role assignment for an application at a resource group scope

Assigns the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role to an application with service principal object ID 77777777-7777-7777-7777-777777777777 at the *pharma-sales* resource group scope.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### Add role assignment for a user at a subscription scope

Assigns the [Reader](built-in-roles.md#reader) role to the *annm\@example.com* user at a subscription scope.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### Add role assignment for a user at a management group scope

Assigns the [Billing Reader](built-in-roles.md#billing-reader) role to the *alain\@example.com* user at a management group scope.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## Remove a role assignment

In Azure RBAC, to remove access, you remove a role assignment by using [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

The following example removes the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role assignment from the *patlong\@contoso.com* user on the *pharma-sales* resource group:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Removes the [Reader](built-in-roles.md#reader) role from the *Ann Mack Team* group with ID 22222222-2222-2222-2222-222222222222 at a subscription scope.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Removes the [Billing Reader](built-in-roles.md#billing-reader) role from the *alain\@example.com* user at the management group scope.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

If you get the error message: "The provided information does not map to a role assignment", make sure that you also specify the `-Scope` or `-ResourceGroupName` parameters. For more information, see [Troubleshoot Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## Next steps

- [List Azure role assignments using Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Grant a group access to Azure resources using Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Manage resources with Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)