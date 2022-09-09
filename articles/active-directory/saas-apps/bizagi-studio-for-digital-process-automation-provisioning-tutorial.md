---
title: 'Tutorial: Configure Bizagi Studio for Digital Process Automation for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and deprovision user accounts from Azure AD to Bizagi Studio for Digital Process Automation.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd

ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
---

# Tutorial: Configure Bizagi Studio for Digital Process Automation for automatic user provisioning

This tutorial describes the steps you need to perform in both Bizagi Studio for Digital Process Automation and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured to do so, Azure AD automatically provisions and deprovisions users and groups to [Bizagi Studio for Digital Process Automation](https://www.bizagi.com/) by using the Azure AD provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../manage-apps/user-provisioning.md). 


## Capabilities supported
> [!div class="checklist"]
> * Create users in Bizagi Studio for Digital Process Automation
> * Remove users in Bizagi Studio for Digital Process Automation when they don't require access anymore
> * Keep user attributes synchronized between Azure AD and Bizagi Studio for Digital Process Automation
> * [Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) to Bizagi Studio for Digital Process Automation (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following:

* [An Azure AD tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* A user account in Azure AD with [permission](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) to configure provisioning. Examples include application administrator, cloud application administrator, application owner, or global administrator. 
* Bizagi Studio for Digital Process Automation version 11.2.4.2X or later.

## Plan your provisioning deployment
Follow these steps for planning:

1. Learn about [how the provisioning service works](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Determine who will be [in scope for provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Determine what data to [map between Azure AD and Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## Configure to support provisioning with Azure AD
To configure Bizagi Studio for Digital Process Automation to support provisioning with Azure AD, follow these steps:

1. Sign in to your work portal as a user with **Admin permissions**.

2. Go to **Admin** > **Security** > **OAuth 2 Applications**.

   ![Screenshot of Bizagi, with OAuth 2 Applications highlighted.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Select **Add**.
4. For **Grant Type**, select **Bearer token**. For **Allowed Scope**, select **API** and **USER SYNC**. Then select **Save**.

   ![Screenshot of Register Application, with Grant Type and Allowed Scope highlighted.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Copy and save the **Client Secret**. In the Azure portal, for your Bizagi Studio for Digital Process Automation application, on the **Provisioning** tab, the client secret value is entered in the **Secret Token** field.

   ![Screenshot of Oauth, with Client Secret highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## Add the application from the Azure AD gallery

To start managing provisioning to Bizagi Studio for Digital Process Automation, add the app from the Azure AD application gallery. If you have previously set up Bizagi Studio for Digital Process Automation for single sign-on, you can use the same application. When you're initially testing the integration, however, you should create a separate app. For more information, see [Quickstart: Add an application to your Azure Active Directory (Azure AD) tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## Define who is in scope for provisioning 

With the Azure AD provisioning service, you can scope who is provisioned based on assignment to the application, based on attributes of the user and group, or both. If you scope based on assignment, see the steps in [Assign or unassign users, and groups, for an app using the Graph API](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you scope based solely on attributes of the user or group, you can use a scoping filter. For more information, see [Attribute-based application provisioning with scoping filters](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

Note the following points about scoping:

* When you're assigning users and groups to Bizagi Studio for Digital Process Automation, you must select a role other than **Default Access**. Users with the default access role are excluded from provisioning, and are marked in the provisioning logs as will be marked as not effectively entitled. If the only role available on the application is the default access role, you can [update the application manifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) to add more roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When the scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When the scope is set to all users and groups, you can specify an [attribute-based scoping filter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## Configure automatic user provisioning 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and groups. You're doing this in your test app, based on user and group assignments in Azure AD.

### Configure automatic user provisioning for Bizagi Studio for Digital Process Automation in Azure AD

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications** > **All applications**.

	![Screenshot of the Azure portal, with Enterprise applications and All applications highlighted.](common/enterprise-applications.png)

2. In the applications list, select **Bizagi Studio for Digital Process Automation**.

3. Select the **Provisioning** tab.

	![Screenshot of Manage options, with Provisioning highlighted.](common/provisioning.png)

4. Set **Provisioning Mode** to **Automatic**.

	![Screenshotof Provisioning Mode control, with Automatic highlighted.](common/provisioning-automatic.png)

5. In the **Admin Credentials** section, enter your tenant URL and secret token for Bizagi Studio for Digital Process Automation. 

      * **Tenant URL:** Enter the Bizagi SCIM endpoint, with the following structure:  `<Your_Bizagi_Project>/scim/v2/`.
         For example: `https://my-company.bizagi.com/scim/v2/`.

      * **Secret token:** This value is retrieved from the step discussed earlier in this article.

      To ensure that Azure AD can connect to Bizagi Studio for Digital Process Automation, select **Test Connection**. If the connection fails, ensure that your Bizagi Studio for Digital Process Automation account has administrator permissions, and try again.

   ![Screenshot of Admin Credentials, with Test Connection highlighted.](common/provisioning-testconnection-tenanturltoken.png)

6. For **Notification Email**, enter the email address of a person or group who should receive the provisioning error notifications. Select the option to **Send an email notification when a failure occurs**.

	![Screenshot of Notification Email options.](common/provisioning-notification-email.png)

7. Select **Save**.

8. In the **Mappings** section, select **Synchronize Azure Active Directory Users to Bizagi Studio for Digital Process Automation**.

9. In the **Attribute-Mapping** section, review the user attributes that are synchronized from Azure AD to Bizagi Studio for Digital Process Automation. The attributes selected as **Matching** properties are used to match the user accounts in Bizagi Studio for Digital Process Automation for update operations. If you change the [matching target attribute](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), you must ensure that the Bizagi Studio for Digital Process Automation API supports filtering users based on that attribute. Select **Save** to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |phoneNumbers[type eq "mobile"].value|String|
   
10. To configure scoping filters, see the [Scoping filter tutorial](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. To enable the Azure AD provisioning service for Bizagi Studio for Digital Process Automation, in the **Settings** section, change the **Provisioning Status** to **On**.

	![Screenshot of Provisioning Status toggle.](common/provisioning-toggle-on.png)

12. Define the users and groups that you want to provision to Bizagi Studio for Digital Process Automation. In the **Settings** section, choose the desired values in **Scope**.

	![Screenshot of Scope options.](common/provisioning-scope.png)

13. When you're ready to provision, select **Save**.

	![Screenshot of Save control.](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Monitor your deployment
After you've configured provisioning, use the following resources to monitor your deployment:

- Use the [provisioning logs](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) to determine which users have been provisioned successfully or unsuccessfully.
- Check the [progress bar](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) to see the status of the provisioning cycle, and how close it is to completion.
- If the provisioning configuration is in an unhealthy state, the application will go into quarantine. For more information, see [Application provisioning in quarantine status](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../manage-apps/check-status-user-account-provisioning.md)
