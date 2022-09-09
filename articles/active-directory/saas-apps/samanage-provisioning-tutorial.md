---
title: 'Tutorial: Configure SolarWinds Service Desk (previously Samanage) for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and de-provision user accounts from Azure AD to SolarWinds Service Desk (previously Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
---

# Tutorial: Configure SolarWinds Service Desk (previously Samanage) for automatic user provisioning

This tutorial describes the steps you need to perform in both SolarWinds Service Desk (previously Samanage) and Azure Active Directory (Azure AD) to configure automatic user provisioning. When configured, Azure AD automatically provisions and de-provisions users and groups to [SolarWinds Service Desk](https://www.samanage.com/pricing/) using the Azure AD Provisioning service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md).

## Migrate to the new SolarWinds Service Desk application

If you have an existing integration with SolarWinds Service Desk, see the following section about upcoming changes. If you're setting up SolarWinds Service Desk for the first time, you can skip this section and move to **Capabilities supported**.

#### What's changing?

* Changes on the Azure AD side: The authorization method to provision users in Samange has historically been **Basic auth**. Soon you will see the authorization method changed to **Long lived secret token**.


#### What do I need to do to migrate my existing custom integration to the new application?

If you have an existing SolarWinds Service Desk integration with valid admin credentials, **no action is required**. We automatically migrate customers to the new application. This process is done completely behind the scenes. If the existing credentials expire, or if you need to authorize access to the application again, you need to generate a long-lived secret token. To generate a new token, refer to Step 2 of this article.


#### How can I tell if my application has been migrated? 

When your application is migrated, in the **Admin Credentials** section, the **Admin Username** and **Admin Password** fields will be replaced with a single **Secret Token** field.

## Capabilities supported

> [!div class="checklist"]
> * Create users in SolarWinds Service Desk
> * Remove users in SolarWinds Service Desk when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and SolarWinds Service Desk
> * Provision groups and group memberships in SolarWinds Service Desk
> * [Single sign-on](./samanage-tutorial.md) to SolarWinds Service Desk (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../users-groups-roles/directory-assign-admin-roles.md) to configure provisioning (for example, Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator). 
* A [SolarWinds Service Desk tenant](https://www.samanage.com/pricing/) with the Professional package.
* A user account in SolarWinds Service Desk with admin permissions.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure SolarWinds Service Desk to support provisioning with Azure AD

To generate a secret token for authentication, see [Tutorial tokens authentication for API integration](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657).

## Step 3. Add SolarWinds Service Desk from the Azure AD application gallery

Add SolarWinds Service Desk from the Azure AD application gallery to start managing provisioning to SolarWinds Service Desk. If you previously set up SolarWinds Service Desk for SSO, you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to SolarWinds Service Desk, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to SolarWinds Service Desk 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in TestApp based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for SolarWinds Service Desk in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **SolarWinds Service Desk**.

3. Select the **Provisioning** tab.

	![Screenshot that shows the Provisioning tab selected.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot that shows Provisioning Mode set to Automatic.](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://api.samanage.com` in **Tenant URL**.  Input the secret token value retrieved earlier in **Secret Token**. Select **Test Connection** to ensure Azure AD can connect to SolarWinds Service Desk. If the connection fails, ensure your SolarWinds Service Desk account has Admin permissions and try again.

	![Screenshot that shows the Test Connection button selected.](./media/samanage-provisioning-tutorial/provisioning.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and select the **Send an email notification when a failure occurs** check box.

	![Notification Email](common/provisioning-notification-email.png)

7. Select **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to SolarWinds Service Desk**.

9. Review the user attributes that are synchronized from Azure AD to SolarWinds Service Desk in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in SolarWinds Service Desk for update operations. If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you will need to ensure that the SolarWinds Service Desk API supports filtering users based on that attribute. Select the **Save** button to commit any changes.

      ![Samange User mappings](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to SolarWinds Service Desk**.

11. Review the group attributes that are synchronized from Azure AD to SolarWinds Service Desk in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in SolarWinds Service Desk for update operations. Select the **Save** button to commit any changes.

      ![Samange Group mappings](./media/samanage-provisioning-tutorial/group-attributes.png)

12. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for SolarWinds Service Desk, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to SolarWinds Service Desk by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

15. When you are ready to provision, select **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. 

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

1. Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
2. Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
3. If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).

## Connector limitations

If you select the **Sync all users and groups** option and configure a value for the SolarWinds Service Desk **roles** attribute, the value under the **Default value if null (is optional)** box must be expressed in the following format:

- {"displayName":"role"}, where role is the default value you want.

## Change log

* 09/14/2020 - Changed the company name in two SaaS tutorials from Samanage to SolarWinds Service Desk (previously Samanage) per https://github.com/ravitmorales.
* 04/22/2020 - Updated authorization method from basic auth to long lived secret token.

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)