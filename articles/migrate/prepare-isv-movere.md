---
title: Prepare Azure Migrate to work with ISV tools/Movere 
description: Prepare Azure Migrate to work with ISV tools/Movere 
ms.topic: how-to
ms.date: 05/07/2020
---

# Prepare to work with ISV tools/Movere

If you've added an [ISV tool](migrate-services-overview.md#isv-integration), or Movere, to an Azure Migrate project, there are a couple of steps to prepare before you link the tool, and send data to Azure Migrate. 

## Check Azure AD permissions

Your Azure user account needs a couple of permissions:

- Permission to register an Azure AD app with your Azure tenant.
- Permission to allocate a role to the Azure AD app at the subscription level.


## Set permissions to register an Azure AD app

1. In Azure Active Directory, check the role for  your account. If you have the user role, click **User settings** on the left and verify whether users can register applications.
2. If it's set to **Yes**, any users in the Azure AD tenant can register an app. If they can't, then only admin users can register apps.
3. If you don't have permissions, an admin user can provide your user account with the [Application Administrator](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator) role, so that you can register the app.
4. After the tool is linked to Azure Migrate, the admin can remove the role from your account.

## Set permissions to assign a role to an Azure AD app
 
In your Azure subscription, your account needs **Microsoft.Authorization/*/Write access**, to assign a role to an Azure AD app. 

1. To check the subscription permissions, in the Azure portal, open **Subscriptions**.
2. Select the relevant subscription. If you don't see it, select the **global subscriptions filter**. 
3. Select **My permissions**. Then, select **Click here to view complete access details for this subscription**.
4. In **Role assignments** > **View**, and check the permissions.
5. If your account doesn't have permissions, ask the subscription administrator to add you to [User Access Administrator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) role, or the [Owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) role.
 

## Start using the tool

1. If you don't yet have a license or free trial for the tool, in the tool entry in Azure Migrate, in **Register**, click **Learn more**.
2. In the tool, follow the instructions, to link from the tool to the Azure Migrate project, and to send data to Azure Migrate.

## Next steps

Follow the ISV or Movere instructions to send data to Azure Migrate.

   
