---
title: Add an existing Azure subscription to your tenant - Azure AD
description: Instructions about how to add an existing Azure subscription to your Azure Active Directory tenant.
services: active-directory
author: ajburnle
manager: daveba

ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: "it-pro, seodec18, contperfq4"
ms.collection: M365-identity-device-management
---

# Associate or add an Azure subscription to your Azure Active Directory tenant

An Azure subscription has a trust relationship with Azure Active Directory (Azure AD). A subscription trusts Azure AD to authenticate users, services, and devices.

Multiple subscriptions can trust the same Azure AD directory. Each subscription can only trust a single directory.

One or more Azure subscriptions can establish a trust relationship with an instance of Azure Active Directory (Azure AD) in order to authenticate and authorize security principals and devices against Azure services.  When a subscription expires, the trusted instance of the Azure AD service remains, but the security principals lose access to Azure resources.

When a user signs up for a Microsoft cloud service, a new Azure AD tenant is created and the user is made a member of the Global Administrator role. However, when an owner of a subscription joins their subscription to an existing tenant, the owner isn't assigned to the Global Administrator role.

All of your users have a single *home* directory for authentication. Your users can also be guests in other directories. You can see both the home and guest directories for each user in Azure AD.

> [!Important]
> When you associate a subscription with a different directory, users that have roles assigned using [Azure role-based access control](../../role-based-access-control/role-assignments-portal.md) lose their access. Classic subscription administrators, including Service Administrator and Co-Administrators, also lose access.
>
> Moving your Azure Kubernetes Service (AKS) cluster to a different subscription, or moving the cluster-owning subscription to a new tenant, causes the cluster to lose functionality due to lost role assignments and service principal's rights. For more information about AKS, see [Azure Kubernetes Service (AKS)](../../aks/index.yml).

## Before you begin

Before you can associate or add your subscription, do the following tasks:

- Review the following list of changes that will occur after you associate or add your subscription, and how you might be affected:

  - Users that have been assigned roles using Azure RBAC will lose their access
  - Service Administrator and Co-Administrators will lose access
  - If you have any key vaults, they'll be inaccessible and you'll have to fix them after association
  - If you have any managed identities for resources such as Virtual Machines or Logic Apps, you must re-enable or recreate them after the association
  - If you have a registered Azure Stack, you'll have to re-register it after association
  - For more information, see [Transfer an Azure subscription to a different Azure AD directory](../../role-based-access-control/transfer-subscription.md).

- Sign in using an account that:

  - Has an [Owner](../../role-based-access-control/built-in-roles.md#owner) role assignment for the subscription. For information about how to assign the Owner role, see [Add or remove Azure role assignments using the Azure portal](../../role-based-access-control/role-assignments-portal.md).
  - Exists in both the current directory and in the new directory. The current directory is associated with the subscription. You'll associate the new directory with the subscription. For more information about getting access to another directory, see [Add Azure Active Directory B2B collaboration users in the Azure portal](../external-identities/add-users-administrator.md).

- Make sure you're not using an Azure Cloud Service Providers (CSP) subscription (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), a Microsoft Internal subscription (MS-AZR-0015P), or a Microsoft Imagine subscription (MS-AZR-0144P).

## Associate a subscription to a directory<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

To associate an existing subscription to your Azure AD directory, follow these steps:

1. Sign in and select the subscription you want to use from the [Subscriptions page in Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Select **Change directory**.

   ![Subscriptions page, with Change directory option highlighted](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Review any warnings that appear, and then select **Change**.

   ![Change the directory page, showing the directory to change to](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   After the directory is changed for the subscription, you will get a success message.

1. Select **Switch directories** on the subscription page to go to your new directory.

   ![Directory switcher page, with sample information](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   It can take several hours for everything to show up properly. If it seems to be taking too long, check the **Global subscription filter**. Make sure the moved subscription isn't hidden. You may need to sign out of the Azure portal and sign back in to see the new directory.

Changing the subscription directory is a service-level operation, so it doesn't affect subscription billing ownership. To delete the original directory, you must transfer the subscription billing ownership to a new Account Admin. To learn more about transferring billing ownership, see [Transfer ownership of an Azure subscription to another account](../../cost-management-billing/manage/billing-subscription-transfer.md).

## Post-association steps

After you associate a subscription to a different directory, you might need to do the following tasks to resume operations:

- If you have any key vaults, you must change the key vault tenant ID. For more information, see [Change a key vault tenant ID after a subscription move](../../key-vault/general/move-subscription.md).

- If you used system-assigned Managed Identities for resources, you must re-enable these identities. If you used user-assigned Managed Identities, you must re-create these identities. After re-enabling or recreating the Managed Identities, you must re-establish the permissions assigned to those identities. For more information, see [What is managed identities for Azure resources?](../managed-identities-azure-resources/overview.md).

- If you've registered an Azure Stack using this subscription, you must re-register. For more information, see [Register Azure Stack with Azure](/azure-stack/operator/azure-stack-registration).

- For more information, see [Transfer an Azure subscription to a different Azure AD directory](../../role-based-access-control/transfer-subscription.md).

## Next steps

- To create a new Azure AD tenant, see [Quickstart: Create a new tenant in Azure Active Directory](active-directory-access-create-new-tenant.md).

- To learn more about how Microsoft Azure controls resource access, see [Classic subscription administrator roles, Azure roles, and Azure AD administrator roles](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- To learn more about how to assign roles in Azure AD, see [Assign administrator and non-administrator roles to users with Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
