---
title: Manage users in Azure FarmBeats
description: This article describes how to manage users in Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
---


# Manage users

Azure FarmBeats includes user management for people who are part of your Azure Active Directory (Azure AD) instance. You can add users to your Azure FarmBeats instance to access the APIs, view the generated maps, and access sensor telemetry from the farm.

## Prerequisites

- An Azure FarmBeats installation is required. For more information, see [Install Azure FarmBeats](install-azure-farmbeats.md).
- The email IDs of the users you want to add or remove from your Azure FarmBeats instance.

## Manage Azure FarmBeats users

Azure FarmBeats uses Azure AD for authentication, access control, and roles. You can add users in the Azure AD tenant as users in Azure FarmBeats.

> [!NOTE]
> If a user you're trying to add as an Azure FarmBeats user isn't present in the Azure AD tenant, complete the setup by following the instructions in the "Add Azure AD users" section.

Azure FarmBeats supports two types of user roles:

 - **Admin**: Full access to Azure FarmBeats Datahub APIs. Users in this role can query all Azure FarmBeats Datahub objects and perform all operations from the FarmBeats Accelerator.
 - **Read-Only**: Read-only access to FarmBeats Datahub APIs. Users can view the Datahub APIs, the Accelerator Dashboards, and the maps. Users with read-only access can't perform operations such as generating maps, associating devices, or creating farms.

## Add users to Azure FarmBeats

To add users to Azure FarmBeats:

1. Sign in to Accelerator, and then select the **Settings** icon.
2. Select **Access Control**.

    ![The Farms Settings pane](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Enter the email ID of the user you want to grant access to.
4. Select the desired role, **Admin** or **Read-Only**.
5. Select **Add Role**.

The added user can now access Azure FarmBeats (both Datahub and Accelerator).

## Delete users from Azure FarmBeats

To remove users from the Azure FarmBeats system:

1. Sign in to Accelerator, and then select the **Settings** icon.
2. Select **Access Control**.
3. Select **Delete**.

   The user is deleted from the system. You will receive the following confirmation message:

   ![Azure FarmBeats confirmation message](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## Add Azure AD users

> [!NOTE]
> Azure FarmBeats users need to exist in the Azure AD tenant before you can assign them to applications and roles. If a user that you want to add to Azure FarmBeats doesn't already exist in the Azure AD tenant, follow the instructions in this section. If the user exists in the Azure AD tenant, you can skip these instructions.

To add users to Azure AD, do the following:

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. At the top right, select your account, and then switch to the Azure AD tenant that's associated with FarmBeats.
3. Select **Azure Active Directory** > **Users**.

    A list of Azure AD users is displayed.

4. To add a user to the directory, select **New user**. To add an external user, select **New guest user**.

    ![The "All users" pane](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Select the new user's name, and then complete the required fields for that user.
6. Select **Create**.

For information about managing Azure AD users, see [Add or delete users in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## Next steps

You have successfully added users to your Azure FarmBeats instance. Now, learn how to [create and manage farms](manage-farms-in-azure-farmbeats.md#create-farms).
