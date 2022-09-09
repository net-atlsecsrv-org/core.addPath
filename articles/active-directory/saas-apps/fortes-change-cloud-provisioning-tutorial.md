---
title: 'Tutorial: Configure Fortes Change Cloud for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to Fortes Change Cloud.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: ef9a8f5e-0bf0-46d6-8e17-3bcf1a5b0a6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2021
ms.author: Zhchia
---

# Tutorial: Configure Fortes Change Cloud for automatic user provisioning

This tutorial describes the steps you need to perform in both Fortes Change Cloud and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [Fortes Change Cloud](https://fortesglobal.com/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities Supported
> [!div class="checklist"]
> * Create users in Fortes Change Cloud
> * Remove users in Fortes Change Cloud when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and Fortes Change Cloud
> * [Single sign-on](fortes-change-cloud-tutorial.md) to Fortes Change Cloud (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A Fortes Change Cloud tenant.
* A user account in Fortes Change Cloud with Admin permissions.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and Fortes Change Cloud](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure Fortes Change Cloud to support provisioning with Azure AD

1. Login with your admin account to Fortes Change Cloud. Click on the **Settings icon** and then navigate to **User Provisioning (SCIM)**.

    [ ![The Fortes Change Cloud SCIM Setting](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png) ](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png#lightbox)

2. In the new window, copy and save the **Tenant URL** and the **Primary token**. The Tenant URL will be entered in the **Tenant URL** * field and primary token will be entered in the **Secret** * Token field in the Provisioning tab of your Fortes Change Cloud application in the Azure portal.
     
      [ ![The Fortes Change Cloud primary token](media/fortes-change-cloud-provisioning-tutorial/primary-token.png)](media/fortes-change-cloud-provisioning-tutorial/primary-token.png#lightbox)

## Step 3. Add Fortes Change Cloud from the Azure AD application gallery

Add Fortes Change Cloud from the Azure AD application gallery to start managing provisioning to Fortes Change Cloud. If you have previously setup Fortes Change Cloud for SSO you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to Fortes Change Cloud, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to Fortes Change Cloud 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in TestApp based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for Fortes Change Cloud in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Fortes Change Cloud**.

	![The Fortes Change Cloud link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Provisioning tab](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab automatic](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input your Fortes Change Cloud Tenant URL and Secret Token. Click **Test Connection** to ensure Azure AD can connect to Fortes Change Cloud. If the connection fails, ensure your Fortes Change Cloud account has Admin permissions and try again.

 	![Token](common/provisioning-testconnection-tenanturltoken.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

7. Select **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Fortes Change Cloud**.

9. Review the user attributes that are synchronized from Azure AD to Fortes Change Cloud in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Fortes Change Cloud for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the Fortes Change Cloud API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:fcc:2.0:User:administrator|Boolean|
   |urn:ietf:params:scim:schemas:extension:fcc:2.0:User:loginDisabled|Boolean|

  

10. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. To enable the Azure AD provisioning service for Fortes Change Cloud, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

12. Define the users and/or groups that you would like to provision to Fortes Change Cloud by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

13. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
* If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).  

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)