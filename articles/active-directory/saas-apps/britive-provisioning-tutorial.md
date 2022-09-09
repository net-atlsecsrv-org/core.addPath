---
title: 'Tutorial: Configure Britive for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
---

# Tutorial: Configure Britive for automatic user provisioning

This tutorial describes the steps you need to perform in both Britive and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [Britive](https://www.britive.com/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities Supported
> [!div class="checklist"]
> * Create users in Britive
> * Remove users in Britive when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and Britive
> * Provision groups and group memberships in Britive
> * [Single sign-on](britive-tutorial.md) to Britive (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A [Britive](https://www.britive.com/) tenant.
* A user account in Britive with Admin permissions.


## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
1. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Determine what data to [map between Azure AD and Britive](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure Britive to support provisioning with Azure AD

The application will have to be manually configured using the steps provided below:
1. Login to Britive application with administrator privileges
1. Click on **Admin->User Administration->Identity Providers**
1. Click on **Add Identity Provider**. Enter the name and description. Click on Add Identity Provider button.

	![Identity Provider](media/britive-provisioning-tutorial/identity.png)

1. A configuration page similar to one displayed below will be shown.

	![Configuration Page](media/britive-provisioning-tutorial/configuration.png)

1. Click on **SCIM** tab. Change the SCIM provider from Generic to Azure and save the changes. Copy the SCIM URL and note it down.This values will be entered in the **Tenant URL** boxes on the Provisioning tab of your Britive application in the Azure portal.

	![SCIM Page](media/britive-provisioning-tutorial/scim.png)

1. Click on **Create Token**. Select the validity of the token as required and click on Create Token button.

	![Create Token](media/britive-provisioning-tutorial/create-token.png)

1. Copy the token generated and note it down. Click OK. Please note that the user will not be able to see the token again. Click on Re-Create button to generate a new token if needed.This values will be entered in the **Secret Token** and Tenant URL boxes on the Provisioning tab of your getAbstract application in the Azure portal.

	![Copy Token](media/britive-provisioning-tutorial/copy-token.png) 


## Step 3. Add Britive from the Azure AD application gallery

Add Britive from the Azure AD application gallery to start managing provisioning to Britive. If you have previously setup Britive for SSO you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to Britive, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to Britive 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Britive based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for Britive in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

1. In the applications list, select **Britive**.

	![The Britive link in the Applications list](common/all-applications.png)

1. Select the **Provisioning** tab.

	![Provisioning tab](common/provisioning.png)

1. Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab automatic](common/provisioning-automatic.png)

1. Under the **Admin Credentials** section, input your Britive Tenant URL and Secret Token. Click **Test Connection** to ensure Azure AD can connect to Britive. If the connection fails, ensure your Britive account has Admin permissions and try again.

 	![Token](common/provisioning-testconnection-tenanturltoken.png)

1. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

1. Select **Save**.

1. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Britive**.

1. Review the user attributes that are synchronized from Azure AD to Britive in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Britive for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the Britive API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;
   |active|Boolean|
   |displayName|String|
   |title|String|
   |externalId|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |nickName|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |emails[type eq "home"].value|String|
   |emails[type eq "other"].value|String|
   |emails[type eq "work"].value|String|
   |phoneNumbers[type eq "home"].value|String|
   |phoneNumbers[type eq "other"].value|String|
   |phoneNumbers[type eq "pager"].value|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |addresses[type eq "work"].formatted|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].country|String|
   |addresses[type eq "home"].formatted|String|
   |addresses[type eq "home"].streetAddress|String|
   |addresses[type eq "home"].locality|String|
   |addresses[type eq "home"].region|String|
   |addresses[type eq "home"].postalCode|String|
   |addresses[type eq "home"].country|String|
   |addresses[type eq "other"].formatted|String|
   |addresses[type eq "other"].streetAddress|String|
   |addresses[type eq "other"].locality|String|
   |addresses[type eq "other"].region|String|
   |addresses[type eq "other"].postalCode|String|
   |addresses[type eq "other"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Reference|


1. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Britive**.

1. Review the group attributes that are synchronized from Azure AD to Britive in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Britive for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|Supported for filtering|
      |---|---|---|
      |displayName|String|&check;
      |externalId|String|
      |members|Reference|

1. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. To enable the Azure AD provisioning service for Britive, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

1. Define the users and/or groups that you would like to provision to Britive by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

1. When you are ready to provision, click **Save**.

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