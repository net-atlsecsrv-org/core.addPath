---
title: 'Tutorial: Configure Webroot Security Awareness Training for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to Webroot Security Awareness Training.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
---

# Tutorial: Configure Webroot Security Awareness Training for automatic user provisioning

This tutorial describes the steps you need to perform in both Webroot Security Awareness Training and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [Webroot Security Awareness Training](https://www.webroot.com/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities supported
> [!div class="checklist"]
> * Create users in Webroot Security Awareness Training
> * Remove users in Webroot Security Awareness Training when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and Webroot Security Awareness Training
> * Provision groups and group memberships in Webroot Security Awareness Training

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../users-groups-roles/directory-assign-admin-roles.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator).
* A Managed Service Provider Console with Webroot Security Awareness Training enabled for at least one of your sites.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and Webroot Security Awareness Training](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure Webroot Security Awareness Training to support provisioning with Azure AD

### Obtain a secret token

To connect your site to Azure AD, you will need to obtain a **Secret Token** for that site in the Webroot management console.

1. Sign into your [Webroot management console](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. From the **Sites** tab, click the gear icon in the Security Awareness Training column for the site you wish to connect with Azure AD.

    ![Gear Icon](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Click the button to **Configure Azure AD Integration**.

    ![Configure Azure AD Integration](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Copy and save the **Secret Token**. This value will be entered in the Secret Token field in the Provisioning tab of your Webroot Security Awareness Training application in the Azure portal.

5. Click **Done**.

    ![Copy Secret Token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## Step 3. Add Webroot Security Awareness Training from the Azure AD application gallery

Add Webroot Security Awareness Training from the Azure AD application gallery to start managing provisioning to Webroot Security Awareness Training. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to Webroot Security Awareness Training, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to Webroot Security Awareness Training 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in TestApp based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for Webroot Security Awareness Training in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Webroot Security Awareness Training**.

	![The Webroot Security Awareness Training link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot of the Provisioning Mode dropdown list with the Automatic option called out.](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://awarenessapi.webrootanywhere.com/api/v2/scim` in **Tenant URL**. Input the secret token value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Webroot Security Awareness Training. If the connection fails, ensure your Webroot Security Awareness Training account has Admin permissions and try again.

 	![Screenshot shows the Admin Credentials dialog box, where you can enter your Tenant U R L and Secret Token.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

7. Select **Save**.

8. Under the **Mappings** section, select **Provision Azure Active Directory Users**.

9. Review the user attributes that are synchronized from Azure AD to Webroot Security Awareness Training in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Webroot Security Awareness Training for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the Webroot Security Awareness Training API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |externalId|String|&check;|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|

10. Under the **Mappings** section, select **Provision Azure Active Directory Groups**.

11. Review the group attributes that are synchronized from Azure AD to Webroot Security Awareness Training in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Webroot Security Awareness Training for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|Supported for filtering|
      |---|---|---|
      |displayName|String|&check;|
      |members|Reference|
      |externalId|String|

12. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for Webroot Security Awareness Training, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Webroot Security Awareness Training by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

15. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

1. Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
2. Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
3. If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).  

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)