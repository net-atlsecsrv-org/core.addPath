---
title: Gain tenant-wide visibility for Azure Security Center | Microsoft Docs
description: This article explains how to manage your security posture at scale by applying policies to all subscriptions linked to your Azure Active Directory tenant.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin

---

# Organize management groups, subscriptions, and tenant-wide visibility

This article explains how to manage your organization’s security posture at scale by applying security policies to all Azure subscriptions linked to your Azure Active Directory tenant.

To get visibility into the security posture of all subscriptions registered in the Azure AD tenant, an Azure role with sufficient read permissions is required to be assigned on the root management group.


## Organize your subscriptions into management groups

### Introduction to management groups

Azure management groups provide the ability to efficiently manage access, policies, and reporting on groups of subscriptions, as well as effectively manage the entire Azure estate by performing actions on the root management group. You can organize subscriptions into management groups and apply your governance policies to the management groups. All subscriptions within a management group automatically inherit the policies applied to the management group. 

Each Azure AD tenant is given a single top-level management group called the **root management group**. This root management group is built into the hierarchy to have all management groups and subscriptions fold up to it. This group allows global policies and Azure role assignments to be applied at the directory level. 

The root management group is created automatically when you do any of the following actions: 
- Open **Management Groups** in the [Azure portal](https://portal.azure.com).
- Create a management group with an API call.
- Create a management group with PowerShell. For PowerShell instructions, see [Create management groups for resource and organization management](../governance/management-groups/create-management-group-portal.md).

Management groups aren't required to onboard Security Center, but we recommend that you create at least one so that the root management group gets created. After the group is created, all subscriptions under your Azure AD tenant will be linked to it. 


For a detailed overview of management groups, see the [Organize your resources with Azure management groups](../governance/management-groups/overview.md) article.

### View and create management groups in the Azure portal

1. From the [Azure portal](https://portal.azure.com), use the search box in the top bar to find and open **Management Groups**.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Accessing your management groups":::

    The list of your management groups appears.

1. To create a management group, select **Add management group**, enter the relevant details, and select **Save**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Adding a management group to Azure":::

    - The **Management Group ID** is the directory unique identifier that is used to submit commands on this management group. This identifier isn't editable after creation as it is used throughout the Azure system to identify this group. 
    - The display name field is the name that is displayed within the Azure portal. A separate display name is an optional field when creating the management group and can be changed at any time.  


### Add subscriptions to a management group
You can add subscriptions to the management group that you created.

1. Under **Management Groups**, select the management group for your subscription.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Select a management group for your subscription":::

1. When the group's page opens, select **Details**

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Opening the details page of a management group":::

1. From the group's details page, select **Add subscription**, then select your subscriptions and select **Save**. Repeat until you've added all the subscriptions in the scope.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Adding a subscription to a management group":::
   > [!IMPORTANT]
   > Management groups can contain both subscriptions and child management  groups. When you assign a user an Azure role to the parent management group, the access is inherited by the child management group's subscriptions. Policies set at the parent management group are also inherited by the children. 


## Grant tenant-wide permissions to yourself

A user with the Azure Active Directory role of **Global Administrator** might have tenant-wide responsibilities, but lack the Azure permissions to view that organization-wide information in Azure Security Center. 

> [!TIP]
> If your organization manages resource access with [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md), or any other PIM tool, the global administrator role must be active for the user making these changes.

To assign yourself tenant-level permissions:

1. As a Global Administrator user without an assignment on the root management group of the tenant, open Security Center's **Overview** page and select the **tenant-wide visibility** link in the banner. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Enable tenant-level permissions in Azure Security Center":::

1. Select the new Azure role to be assigned. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Form for defining the tenant-level permissions to be assigned to your user":::

    > [!TIP]
    > Generally, the Security Admin role is required to apply policies on the root level, while Security Reader will suffice to provide tenant-level visibility. For more information about the permissions granted by these roles, see the [Security Admin built-in role description](../role-based-access-control/built-in-roles.md#security-admin) or the [Security Reader built-in role description](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > For differences between these roles specific to Security Center, see the table in [Roles and allowed actions](security-center-permissions.md#roles-and-allowed-actions).

    The organizational-wide view is achieved by granting roles on the root management group level of the tenant.  

1. Log out of the Azure portal, and then log back in again.

1. Once you have elevated access, open or refresh Azure Security Center to verify you have visibility into all subscriptions under your Azure AD tenant. 

## Assign Azure roles to other users

### Assign Azure roles to users through the Azure portal: 
1. Sign in to the [Azure portal](https://portal.azure.com). 
1. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
1.  Select a management group and select **details**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Management Groups details screenshot":::

1. Select **Access control (IAM)** then **Role assignments**.
1. Select **Add role assignment**.
1. Select the role to assign and the user, then select **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)

### Assign Azure roles to users with PowerShell: 
1. Install [Azure PowerShell](/powershell/azure/install-az-ps).
2. Run the following commands: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. When prompted, sign in with global admin credentials. 

    ![Sign in prompt screenshot](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Grant reader role permissions by running the following command:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. To remove the role, use the following command: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## Remove elevated access 
Once the Azure roles have been assigned to the users, the tenant administrator should remove itself from the user access administrator role.

1. Sign in to the [Azure portal](https://portal.azure.com) or the [Azure Active Directory admin center](https://aad.portal.azure.com).

2. In the navigation list, select **Azure Active Directory** and then select **Properties**.

3. Under **Access management for Azure resources**, set the switch to **No**.

4. To save your setting, select **Save**.



## Next steps
In this article, you learned how to gain tenant-wide visibility for Azure Security Center. For related information, see:

- [Permissions in Azure Security Center](security-center-permissions.md)