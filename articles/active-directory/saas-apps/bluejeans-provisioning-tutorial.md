---
title: 'Tutorial: Configure BlueJeans for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
---

# Tutorial: Configure BlueJeans for automatic user provisioning

This tutorial describes the steps you need to perform in both BlueJeans and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users to [BlueJeans](https://www.bluejeans.com/pricing) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 

## Capabilities supported
> [!div class="checklist"]
> * Create users in BlueJeans
> * Remove users in BlueJeans when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and BlueJeans
> * [Single sign-on](./bluejeans-tutorial.md) to BlueJeans (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md).
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A BlueJeans tenant with [My Company](https://www.bluejeans.com/pricing) plan or better enabled.
* A user account in BlueJeans with Admin permissions.
* SCIM provisioning enabled in BlueJeans Enterprise.

> [!NOTE]
> The Azure AD provisioning integration relies on the [BlueJeans API](https://BlueJeans.github.io/developer), which is available to BlueJeans teams on the Standard plan or better.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and BlueJeans](../app-provisioning/customize-application-attributes.md).

## Step 2. Configure BlueJeans to support provisioning with Azure AD

1. Login to BlueJeans admin console. Navigate to Group Settings > Security.
2. Select **Single Sign On** and **Configure Now**.

	![now](./media/bluejeans-provisioning-tutorial/configure.png)

3. In the **Provision & Integration** window, select **Create and manage user accounts through IDP** and click **GENERATE TOKEN**.

	![generate](./media/bluejeans-provisioning-tutorial/token.png)
	
4. Copy and save the Token. 
5. The BlueJeans Tenant URL is `https://api.bluejeans.com/v2/scim`. The **Tenant URL** and the **Secret Token** from the previous step will be entered in the Provisioning tab of your BlueJeans application in the Azure portal.

## Step 3. Add BlueJeans from the Azure AD application gallery

Add BlueJeans from the Azure AD application gallery to start managing provisioning to BlueJeans. If you have previously setup BlueJeans for SSO you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md).

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users to the application. If you choose to scope who will be provisioned based solely on attributes of the user, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users to BlueJeans, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users before rolling out to everyone. When scope for provisioning is set to assigned users, you can control this by assigning one or two users to the app. When scope is set to all users, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## Step 5. Configure automatic user provisioning to BlueJeans

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users in TestApp based on user assignments in Azure AD.

### To configure automatic user provisioning for BlueJeans in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **BlueJeans**.

	![The BlueJeans link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab automatic](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input your BlueJeans Tenant URL and Secret Token retrieved in Step 2. Click **Test Connection** to ensure Azure AD can connect to BlueJeans. If the connection fails, ensure your BlueJeans account has Admin permissions and try again.

 	![Token](common/provisioning-testconnection-tenanturltoken.png)


6. In the **Notification Email** field, enter the email address of a person who should receive the provisioning error notifications and check the checkbox - **Send an email notification when a failure occurs**.

	![Notification Email](common/provisioning-notification-email.png)

7. Select **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to BlueJeans**.

9. Review the user attributes that are synchronized from Azure AD to BlueJeans in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in BlueJeans for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the BlueJeans API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

|Attribute|Type|Supported for filtering|
|---|---|---|
|userName|String|&check;|
|active|Boolean|
|title|String|
|emails[type eq "work"].value|String|
|name.givenName|String|
|name.familyName|String|
|phoneNumbers[type eq "work"].value|String|
|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|

10. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. To enable the Azure AD provisioning service for BlueJeans, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

12. Define the users that you would like to provision to BlueJeans by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

13. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization of all users defined in **Scope** in the **Settings** section. The initial sync takes longer to perform than subsequent syncs, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

1. Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
2. Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
3. If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).  

## Connector Limitations

* Bluejeans does not allow userNames that exceed 30 characters.

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)