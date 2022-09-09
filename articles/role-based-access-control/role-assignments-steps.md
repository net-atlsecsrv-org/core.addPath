---
title: Steps to add a role assignment - Azure RBAC
description: Learn the steps to assign Azure roles to users, groups, service principals, or managed identities using Azure role-based access control (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
---

# Steps to add a role assignment

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] This article describes the high-level steps to add a role assignment using the [Azure portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), [Azure CLI](role-assignments-cli.md), or the [REST API](role-assignments-rest.md).

## Step 1: Determine who needs access

You first need to determine who needs access. You can assign a role to a user, group, service principal, or managed identity. This is also called a *security principal*.

![Security principal for a role assignment](./media/shared/rbac-security-principal.png)

- User - An individual who has a profile in Azure Active Directory. You can also assign roles to users in other tenants. For information about users in other organizations, see [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Group - A set of users created in Azure Active Directory. When you assign a role to a group, all users within that group have that role. 
- Service principal - A security identity used by applications or services to access specific Azure resources. You can think of it as a *user identity* (username and password or certificate) for an application.
- Managed identity - An identity in Azure Active Directory that is automatically managed by Azure. You typically use [managed identities](../active-directory/managed-identities-azure-resources/overview.md) when developing cloud applications to manage the credentials for authenticating to Azure services.

## Step 2: Find the appropriate role

Permissions are grouped together into a *role definition*. It's typically just called a *role*. You can select from a list of several built-in roles. If the built-in roles don't meet the specific needs of your organization, you can create your own custom roles.

![Role definition for a role assignment](./media/shared/rbac-role-definition.png)

The following lists four fundamental built-in roles. The first three apply to all resource types.

- [Owner](built-in-roles.md#owner) - Has full access to all resources including the right to delegate access to others.
- [Contributor](built-in-roles.md#contributor) - Can create and manage all types of Azure resources but can't grant access to others.
- [Reader](built-in-roles.md#reader) - Can view existing Azure resources.
- [User Access Administrator](built-in-roles.md#user-access-administrator) - Lets you manage user access to Azure resources.

The rest of the built-in roles allow management of specific Azure resources. For example, the [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) role allows a user to create and manage virtual machines.

1. Begin with the comprehensive article, [Azure built-in roles](built-in-roles.md). The table at the top of the article is an index into the details later in the article.

1. In that article, navigate to the service category (such as compute, storage, and databases) for the resource to which you want to grant permissions. The easiest way to find what your looking for is typically to search the page for a relevant keyword, like "blob", "virtual machine", and so on.

1. Review the roles listed for the service category and identify the specific operations you need. Again, always start with the most restrictive role.

    For example, if a security principal needs to read blobs in an Azure storage account, but doesn't need write access, then choose [Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) rather than [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) (and definitely not the administrator-level [Storage Blob Data Owner](built-in-roles.md#storage-blob-data-owner) role). You can always update the role assignments later as needed.

1. If you don't find a suitable role, you can create a [custom role](custom-roles.md).

## Step 3: Identify the needed scope

*Scope* is the set of resources that the access applies to. In Azure, you can specify a scope at four levels: [management group](../governance/management-groups/overview.md), subscription, [resource group](../azure-resource-manager/management/overview.md#resource-groups), and resource. Scopes are structured in a parent-child relationship. Each level of hierarchy makes the scope more specific. You can assign roles at any of these levels of scope. The level you select determines how widely the role is applied. Lower levels inherit role permissions from higher levels. 

![Scope for a role assignment](./media/shared/rbac-scope.png)

When you assign a role at a parent scope, those permissions are inherited to the child scopes. For example:

- If you assign the [Reader](built-in-roles.md#reader) role to a user at the management group scope, that user can read everything in all subscriptions in the management group.
- If you assign the [Billing Reader](built-in-roles.md#billing-reader) role to a group at the subscription scope, the members of that group can read billing data for every resource group and resource in the subscription.
- If you assign the [Contributor](built-in-roles.md#contributor) role to an application at the resource group scope, it can manage resources of all types in that resource group, but not other resource groups in the subscription.

 For more information, see [Understand scope](scope-overview.md).

## Step 4. Check your prerequisites

To assign roles, you must be signed in with a user that is assigned a role that has role assignments write permission, such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator) at the scope you are trying to assign the role. Similarly, to remove a role assignment, you must have the role assignments delete permission.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

If your user account doesn't have permission to assign a role within your subscription, you see an error message that your account "does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write'." In this case, contact the administrators of your subscription as they can assign the permissions on your behalf.

## Step 5. Add role assignment

Once you know the security principal, role, and scope, you can assign the role. You can add role assignments using the Azure portal, Azure PowerShell, Azure CLI, Azure SDKs, or REST APIs. You can have up to **2000** role assignments in each subscription. This limit includes role assignments at the subscription, resource group, and resource scopes. You can have up to **500** role assignments in each management group.

Check out the following articles for detailed steps for how to add role assignments.

- [Add or remove Azure role assignments using the Azure portal](role-assignments-portal.md)
- [Add or remove Azure role assignments using Azure PowerShell](role-assignments-powershell.md)
- [Add or remove Azure role assignments using Azure CLI](role-assignments-cli.md)
- [Add or remove Azure role assignments using the REST API](role-assignments-rest.md)

## Next steps

- [Tutorial: Grant a user access to Azure resources using the Azure portal](quickstart-assign-role-user-portal.md)