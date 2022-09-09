---
title: Remove limits on creating app registrations - Azure AD | Microsoft Docs
description: Assign a custom role to grant unrestricted app registrations in the Azure AD Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell

ms.collection: M365-identity-device-management
---
# Quickstart: Grant permission to create unlimited app registrations

In this quick start guide, you will create a custom role with permission to create an unlimited number of app registrations, and then assign that role to a user. The assigned user can then use the Azure AD portal, Azure AD PowerShell, or Microsoft Graph API to create application registrations. Unlike the built-in Application Developer role, this custom role grants the ability to create an unlimited number of application registrations. The Application Developer role grants the ability, but the total number of created objects is limited to 250 to prevent hitting [the directory-wide object quota](../enterprise-users/directory-service-limits-restrictions.md). The least privileged role required to create and assign Azure AD custom roles is the Privileged Role administrator.

If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Create a custom role using the Azure AD portal

1. Sign in to the [Azure AD admin center](https://aad.portal.azure.com) with Privileged Role administrator or Global administrator permissions in the Azure AD organization.
1. Select **Azure Active Directory**, select **Roles and administrators**, and then select **New custom role**.

    ![Create or edit roles from the Roles and administrators page](./media/quickstart-app-registration-limits/new-custom-role.png)

1. On the **Basics** tab, provide "Application Registration Creator" for the name of the role and "Can create an unlimited number of application registrations" for the role description, and then select **Next**.

    ![provide a name and description for a custom role on the Basics tab](./media/quickstart-app-registration-limits/basics-tab.png)

1. On the **Permissions** tab, enter "microsoft.directory/applications/create" in the search box, and then select the checkboxes next to the desired permissions, and then select **Next**.

    ![Select the permissions for a custom role on the Permissions tab](./media/quickstart-app-registration-limits/permissions-tab.png)

1. On the **Review + create** tab, review the permissions and select **Create**.

### Assign the role in the Azure AD portal

1. Sign in to the [Azure AD admin center](https://aad.portal.azure.com) with Privileged role administrator or Global administrator permissions in your Azure AD organization.
1. Select **Azure Active Directory** and then select **Roles and administrators**.
1. Select the Application Registration Creator role and select **Add assignment**.
1. Select the desired user and click **Select** to add the user to the role.

Done! In this quickstart, you successfully created a custom role with permission to create an unlimited number of app registrations, and then assign that role to a user.

> [!TIP]
> To assign the role to an application using the Azure AD portal, enter the name of the application into the search box of the assignment page. Applications are not shown in the list by default, but are returned in search results.

### App registration permissions

There are two permissions available for granting the ability to create application registrations, each with different behavior.

- microsoft.directory/applications/createAsOwner: Assigning this permission results in the creator being added as the first owner of the created app registration, and the created app registration will count against the creator's 250 created objects quota.
- microsoft.directory/applications/create: Assigning this permission results in the creator not being added as the first owner of the created app registration, and the created app registration will not count against the creator's 250 created objects quota. Use this permission carefully, because there is nothing preventing the assignee from creating app registrations until the directory-level quota is hit. If both permissions are assigned, this permission takes precedence.

## Create a custom role in Azure AD PowerShell

### Prepare PowerShell

First, install the Azure AD PowerShell module from the [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Then import the Azure AD PowerShell preview module, using the following command:

```powershell
import-module azureadpreview
```

To verify that the module is ready to use, match the version returned by the following command to the one listed here:

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### Create the custom role in Azure AD PowerShell

Create a new role using the following PowerShell script:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### Assign the role in Azure AD PowerShell

Assign the role using the following PowerShell script:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## Create a custom role in the Microsoft Graph API

HTTP request to create the custom role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Body

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### Assign the role in the Microsoft Graph API

The role assignment combines a security principal ID (which can be a user or service principal), a role definition (role) ID, and an Azure AD resource scope.

HTTP request to assign a custom role.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## Next steps

- Feel free to share with us on the [Azure AD administrative roles forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- For more about Azure AD role assignments, see [Assign administrator roles](permissions-reference.md).
- For more about default user permissions, see [comparison of default guest and member user permissions](../fundamentals/users-default-permissions.md).
