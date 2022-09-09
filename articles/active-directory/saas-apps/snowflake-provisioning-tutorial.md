---
title: 'Tutorial: Configure Snowflake for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Snowflake.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
---

# Tutorial: Configure Snowflake for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Snowflake and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to [Snowflake](https://www.Snowflake.com/pricing/). For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> This connector is currently in Public Preview. For more information on the general Microsoft Azure terms of use for Preview features, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Capabilities supported
> [!div class="checklist"]
> * Create users in Snowflake
> * Remove users in Snowflake when they do not require access anymore
> * Keep user attributes synchronized between Azure AD and Snowflake
> * Provision groups and group memberships in Snowflake
> * [Single sign-on](./snowflake-tutorial.md) to Snowflake (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md).
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (e.g. Application Administrator, Cloud Application administrator, Application Owner, or Global Administrator).
* [A Snowflake tenant](https://www.Snowflake.com/pricing/).
* A user account in Snowflake with Admin permissions.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and Snowflake](../app-provisioning/customize-application-attributes.md). 

## Step 2. Configure Snowflake to support provisioning with Azure AD

Before configuring Snowflake for automatic user provisioning with Azure AD, you will need to enable SCIM provisioning on Snowflake.

1. Sign in to your Snowflake Admin Console. Enter the query shown below in the worksheet highlighted and click **Run**.

	![Snowflake Admin Console](media/Snowflake-provisioning-tutorial/image00.png)

2.  A SCIM Access Token will be generated for your Snowflake tenant. To retrieve it, click on the link highlighted below.

	![Screenshot of a worksheet in the Snowflake U I with the S C I M Access token called out.](media/Snowflake-provisioning-tutorial/image01.png)

3. Copy the generated token value and click **Done**. This value will be entered in the **Secret Token** field in the Provisioning tab of your Snowflake application in the Azure portal.

	![Screenshot of the Details section showing the token copied into the text field and the Done option called out.](media/Snowflake-provisioning-tutorial/image02.png)

## Step 3. Add Snowflake from the Azure AD application gallery

Add Snowflake from the Azure AD application gallery to start managing provisioning to Snowflake. If you have previously setup Snowflake for SSO you can use the same application. However it is recommended that you create a separate app when testing out the integration initially. Learn more about adding an application from the gallery [here](../manage-apps/add-application-portal.md). 

## Step 4. Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application and or based on attributes of the user / group. If you choose to scope who will be provisioned to your app based on assignment, you can use the following [steps](../manage-apps/assign-user-or-group-access-portal.md) to assign users and groups to the application. If you choose to scope who will be provisioned based solely on attributes of the user or group, you can use a scoping filter as described [here](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* When assigning users and groups to Snowflake, you must select a role other than **Default Access**. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the default access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add additional roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When scope is set to all users and groups, you can specify an [attribute based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5. Configure automatic user provisioning to Snowflake 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Snowflake based on user and/or group assignments in Azure AD.

### To configure automatic user provisioning for Snowflake in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Snowflake**.

	![The Snowflake link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot of the Provisioning Mode dropdown list with the Automatic option called out.](common/provisioning-automatic.png)

5. Under the Admin Credentials section, input the **SCIM 2.0 base URL and Authentication Token** values retrieved earlier in **Tenant URL** and **Secret Token** fields respectively. Click **Test Connection** to ensure Azure AD can connect to Snowflake. If the connection fails, ensure your Snowflake account has Admin permissions and try again.

	![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and check the checkbox - **Send an email notification when a failure occurs**.

	![Notification Email](common/provisioning-notification-email.png)

8. Click **Save**.

9. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Snowflake**.

10. Review the user attributes that are synchronized from Azure AD to Snowflake in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Snowflake for update operations. Select the **Save** button to commit any changes.

   |Attribute|Type|
   |---|---|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|String|

11. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Snowflake**.

12. Review the group attributes that are synchronized from Azure AD to Snowflake in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Snowflake for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|
      |---|---|
      |displayName|String|
      |members|Reference|

13. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. To enable the Azure AD provisioning service for Snowflake, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

15. Define the users and/or groups that you would like to provision to Snowflake by choosing the desired values in **Scope** in the **Settings** section. If this option is not available, please configure the required fields under Admin Credentials, Click **Save** and refresh the page. 

	![Provisioning Scope](common/provisioning-scope.png)

16. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

	This operation starts the initial synchronization of all users and/or groups defined in **Scope** in the **Settings** section. The initial sync takes longer to perform than subsequent syncs, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running.

## Step 6. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

1. Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
2. Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
3. If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).  

## Connector limitations

* Snowflake generated SCIM tokens expire in 6 months. Be aware that these need to be refreshed before they expire to allow the provisioning syncs to continue working. 

## Change Log

* 07/21/2020 - Enabled soft-delete for all users (via the active attribute).

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps
* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md).