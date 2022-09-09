---
title: 'Tutorial: Configure kpifire for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to kpifire.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: 8c5dd093-20da-4ff6-a9b2-8071f44accd6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2021
ms.author: Zhchia
---

# Tutorial: Configure kpifire for automatic user provisioning

This tutorial describes the steps you need to perform in both kpifire and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [kpifire](https://www.kpifire.com/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities Supported
> [!div class="checklist"]
> * Create users in kpifire
> * Remove users in kpifire when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and kpifire
> * Provision groups and group memberships in kpifire
> * [Single sign-on](kpifire-tutorial.md) to kpifire (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A [kpifire tenant](https://www.kpifire.com/).
* A user account in kpifire with Admin permissions.

## Step 1. Plan your provisioning deployment

1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
1. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine what data to [map between Azure AD and kpifire](../app-provisioning/customize-application-attributes.md).

## Step 2. Configure kpifire to support provisioning with Azure AD
1. Sign in to https://app.kpifire.com with admin rights
1. Navigate to **Settings->API Settings->Add New Token** to generate the SCIM token.

	[ ![kpifire token generation](media/kpifire-provisioning-tutorial/kpifire-token-generation.png) ](media/kpifire-provisioning-tutorial/kpifire-token-generation.png#lightbox)

1. Copy and save the SCIM token. This value will be entered in the **Secret Token** field in the Provisioning tab of your kpifire application in the Azure portal. 


## Step 3. Add kpifire from the Azure AD application gallery

Add kpifire from the Azure AD application gallery to start managing provisioning to kpifire. If you have previously setup kpifire for SSO, you can use the same application. However it's recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to kpifire, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add more roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to kpifire 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in kpifire app based on user and group assignments in Azure AD.

### To configure automatic user provisioning for kpifire in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

1. In the applications list, select **kpifire**.

	![The kpifire link in the Applications list](common/all-applications.png)

1. Select the **Provisioning** tab.

	![Provisioning tab](common/provisioning.png)

1.  Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab automatic](common/provisioning-automatic.png)

1. In the **Admin Credentials** section, enter your kpifire **Tenant URL** and **Secret token** information. Select **Test Connection** to ensure that Azure AD can connect to kpifire. If the connection fails, ensure that your kpifire account has admin permissions and try again.

 	![Token](common/provisioning-testconnection-tenanturltoken.png)

1. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications. Select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

1. Select **Save**.

1. In the **Mappings** section, select **Synchronize Azure Active Directory Users to kpifire**.

1. Review the user attributes that are synchronized from Azure AD to kpifire in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in kpifire for update operations. If you change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you'll need to ensure that the kpifire API supports filtering users based on that attribute. Select **Save** to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|


1. In the **Mappings** section, select **Synchronize Azure Active Directory Groups to kpifire**.

1. Review the group attributes that are synchronized from Azure AD to kpifire in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in kpifire for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|Supported for filtering|
      |---|---|---|
      |displayName|String|&check;
      |members|Reference|     

1. To configure scoping filters, see the instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. To enable the Azure AD provisioning service for kpifire, change **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

1. Define the users or groups that you want to provision to kpifire by selecting the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

1. When you're ready to provision, select **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to do than next cycles, which occur about every 40 minutes as long as the Azure AD provisioning service is running.

## Step 6. Monitor your deployment

After you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users were provisioned successfully or unsuccessfully.
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it's to completion.
* If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. To learn more about quarantine states, see [Application provisioning status of quarantine](../app-provisioning/application-provisioning-quarantine-status.md).

## More resources

* [Managing user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)