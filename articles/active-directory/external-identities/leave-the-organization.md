---

title: Leave an organization as a guest user - Azure Active Directory
description: Shows how an Azure AD B2B guest user can leave an organization by using the Access Panel.

services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/05/2021

ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal

ms.collection: M365-identity-device-management
---

# Leave an organization as a guest user

An Azure Active Directory (Azure AD) B2B guest user can decide to leave an organization at any time if they no longer need to use apps from that organization or maintain any association. A user can leave an organization on their own, without having to contact an administrator.

> [!NOTE]
> A guest user can't leave an organization if their account is disabled in either the home tenant or the resource tenant. If their account is disabled, the guest user will need to contact the tenant admin, who can either delete the guest account or enable the guest account so the user can leave the organization.

## Leave an organization

To leave an organization, follow these steps.

1. Go to your Access Panel Profile page by doing one of the following steps:
   
   - In the [Azure portal](https://portal.azure.com), click your name in the upper right and select **View account**.
   - Open your [Access Panel](https://myapps.microsoft.com), click your name in the upper right, and next to **Organizations** and select  **View account**.
 
 
2. Select **Manage Organizations**.
  ![Screenshot showing user settings in Access Panel](media/leave-the-organization/manage-organizations.png) 
  
3. Under **Organizations**, find the organization that you want to leave, and select **Leave organization**.

   ![Screenshot showing Leave organization option in the user interface](media/leave-the-organization/leave-org.png)
4. When asked to confirm, select **Leave**.
> [!NOTE]
   > You cannot leave your home organization.

## Account removal

When a user leaves an organization, the user account is "soft deleted" in the directory. By default, the user object moves to the **Deleted users** area in Azure AD but isn't permanently deleted for 30 days. This soft deletion enables the administrator to restore the user account (including groups and permissions), if the user makes a request to restore the account within the 30-day period.

If desired, a tenant administrator can permanently delete the account at any time during the 30-day period. To do this:

1. In the [Azure portal](https://portal.azure.com), select **Azure Active Directory**.
2. Under **Manage**, select **Users**.
3. Select **Deleted users**.
4. Select the check box next to a deleted user, and then select **Delete permanently**.

If you permanently delete a user, this action is irrevocable.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## Next steps

- For an overview of Azure AD B2B, see [What is Azure AD B2B collaboration?](what-is-b2b.md)



