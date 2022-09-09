---
title: 'Tutorial: Configure Grammarly for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to Grammarly.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
---

# Tutorial: Configure Grammarly for automatic user provisioning

This tutorial describes the steps you need to perform in both Grammarly and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [Grammarly](https://www.grammarly.com/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../manage-apps/user-provisioning.md). 


## Capabilities Supported
> [!div class="checklist"]
> * Create users in Grammarly
> * Remove users in Grammarly when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and Grammarly

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* A user account in Azure AD with [permission](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) to configure provisioning (e.g. Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A Grammarly Business account with admin access.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Determine who will be in [scope for provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Determine what data to [map between Azure AD and Grammarly](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## Step 2. Configure Grammarly to support provisioning with Azure AD

Reach out to your Grammarly representative, or write to <support@grammarly.com> to request for your provisioning token.

## Step 3. Add Grammarly from the Azure AD application gallery

Add Grammarly from the Azure AD application gallery to start managing provisioning to Grammarly. If you've previously set up Grammarly for SSO, you can use the same application. We recommend that you create a separate app when you test out the integration initially. To learn more about how to add an application from the gallery, see [this quickstart](../manage-apps/add-application-portal.md).

## Step 4. Define who will be in scope for provisioning

You can use the Azure AD provisioning service to scope who will be provisioned based on assignment to the application or based on attributes of the user or group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described in [Provision apps with scoping filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* When you assign users and groups to Grammarly, you must select a role other than **Default Access**. Users with the default access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add more roles.

* Start small. Test with a small set of users and groups before you roll out to everyone. When scope for provisioning is set to assigned users and groups, you can control this option by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute-based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## Step 5. Configure automatic user provisioning to Grammarly

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users or groups in TestApp based on user or group assignments in Azure AD.

### Configure automatic user provisioning for Grammarly in Azure AD

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise applications** > **All applications**.

	![Screenshot that shows the Enterprise applications pane.](common/enterprise-applications.png)

1. In the list of applications, select **Grammarly**.

	![Screenshot that shows the Grammarly link in the list of applications.](common/all-applications.png)

1. Select the **Provisioning** tab.

	![Screenshot that shows the Provisioning tab.](common/provisioning.png)

1. Set **Provisioning Mode** to **Automatic**.

	![Screenshot that shows Provisioning Mode set to Automatic.](common/provisioning-automatic.png)

1. Under the **Admin Credentials** section, in the enter **Tenant URL** field enter `https://sso.grammarly.com/scim/v2`, and in the **Secret Token** field enter the token provided by Grammarly (see Step 2 above). Click **Test Connection** to ensure Azure AD can connect to Grammarly. If the connection fails, ensure your Grammarly account has Admin permissions and try again.

 	![Screenshot that shows the Tenant URL and Secret Token boxes.](common/provisioning-testconnection-tenanturltoken.png)

1. In the **Notification Email** box, enter the email address of a person or group who should receive the provisioning error notifications. Select the **Send an email notification when a failure occurs** check box.

	![Screenshot that shows the Notification Email box.](common/provisioning-notification-email.png)

1. Select **Save**.

1. In the **Mappings** section, select **Synchronize Azure Active Directory Users to Grammarly**.

1. Review the user attributes that are synchronized from Azure AD to Grammarly in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Grammarly for update operations. If you change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you'll need to ensure that the Grammarly API supports filtering users based on that attribute. Select **Save** to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;|
   |externalId|String|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|


1. To configure scoping filters, see the instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. To enable the Azure AD provisioning service for Grammarly, change **Provisioning Status** to **On** in the **Settings** section.

	![Screenshot that shows the Provisioning Status toggled On.](common/provisioning-toggle-on.png)

1. Define the users or groups that you want to provision to Grammarly by selecting the desired values in **Scope** in the **Settings** section.

	![Screenshot that shows the Provisioning Scope.](common/provisioning-scope.png)

1. When you're ready to provision, select **Save**.

	![Screenshot that shows the Save button.](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur about every 40 minutes as long as the Azure AD provisioning service is running.

## Step 6. Monitor your deployment

After you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users were provisioned successfully or unsuccessfully.
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion.
* If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. To learn more about quarantine states, see [Application provisioning status of quarantine](../app-provisioning/application-provisioning-quarantine-status.md).

## Additional resources

* [Managing user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)
