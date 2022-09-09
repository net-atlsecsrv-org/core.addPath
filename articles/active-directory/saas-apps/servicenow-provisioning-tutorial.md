---
title: 'Tutorial: Configure ServiceNow for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to automatically provision and deprovision user accounts from Azure AD to ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
---

# Tutorial: Configure ServiceNow for automatic user provisioning

This tutorial describes the steps that you perform in both ServiceNow and Azure Active Directory (Azure AD) to configure automatic user provisioning. When Azure AD is configured, it automatically provisions and deprovisions users and groups to [ServiceNow](https://www.servicenow.com/) by using the Azure AD provisioning service. 

For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md). 


## Capabilities supported
> [!div class="checklist"]
> * Create users in ServiceNow
> * Remove users in ServiceNow when they don't need access anymore
> * Keep user attributes synchronized between Azure AD and ServiceNow
> * Provision groups and group memberships in ServiceNow
> * Allow [single sign-on](servicenow-tutorial.md) to ServiceNow (recommended)

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* [An Azure AD tenant](../develop/quickstart-create-new-tenant.md) 
* A user account in Azure AD with [permission](../roles/permissions-reference.md) to configure provisioning (Application Administrator, Cloud Application Administrator, Application Owner, or Global Administrator)
* A [ServiceNow instance](https://www.servicenow.com/) of Calgary or higher
* A [ServiceNow Express instance](https://www.servicenow.com/) of Helsinki or higher
* A user account in ServiceNow with the admin role

## Step 1: Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and ServiceNow](../app-provisioning/customize-application-attributes.md). 

## Step 2: Configure ServiceNow to support provisioning with Azure AD

1. Identify your ServiceNow instance name. You can find the instance name in the URL that you use to access ServiceNow. In the following example, the instance name is **dev35214**.

   ![Screenshot that shows a ServiceNow instance.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Obtain credentials for an admin in ServiceNow. Go to the user profile in ServiceNow and verify that the user has the admin role. 

   ![Screenshot that shows a ServiceNow admin role.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## Step 3: Add ServiceNow from the Azure AD application gallery

Add ServiceNow from the Azure AD application gallery to start managing provisioning to ServiceNow. If you previously set up ServiceNow for single sign-on (SSO), you can use the same application. However, we recommend that you create a separate app when you're testing the integration. [Learn more about adding an application from the gallery](../manage-apps/add-application-portal.md). 

## Step 4: Define who will be in scope for provisioning 

The Azure AD provisioning service allows you to scope who will be provisioned based on assignment to the application, or based on attributes of the user or group. If you choose to scope who will be provisioned to your app based on assignment, you can use the [steps to assign users and groups to the application](../manage-apps/assign-user-or-group-access-portal.md). If you choose to scope who will be provisioned based solely on attributes of the user or group, you can [use a scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Keep these tips in mind:

* When you're assigning users and groups to ServiceNow, you must select a role other than Default Access. Users with the Default Access role are excluded from provisioning and will be marked as not effectively entitled in the provisioning logs. If the only role available on the application is the Default Access role, you can [update the application manifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) to add more roles. 

* Start small. Test with a small set of users and groups before rolling out to everyone. When the scope for provisioning is set to assigned users and groups, you can control this by assigning one or two users or groups to the app. When the scope is set to all users and groups, you can specify an [attribute-based scoping filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## Step 5: Configure automatic user provisioning to ServiceNow 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and groups in TestApp. You can base the configuration on user and group assignments in Azure AD.

To configure automatic user provisioning for ServiceNow in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise applications** > **All applications**.

	![Screenshot that shows the Enterprise applications pane.](common/enterprise-applications.png)

2. In the list of applications, select **ServiceNow**.

	![Screenshot that shows a list of applications.](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set **Provisioning Mode** to **Automatic**.

	![Screenshot of the Provisioning Mode drop-down list with the Automatic option called out.](common/provisioning-automatic.png)

5. In the **Admin Credentials** section, enter your ServiceNow admin credentials and username. Select **Test Connection** to ensure that Azure AD can connect to ServiceNow. If the connection fails, ensure that your ServiceNow account has admin permissions and try again.

 	![Screenshot that shows the Service Provisioning page, where you can enter admin credentials.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. In the **Notification Email** box, enter the email address of a person or group that should receive the provisioning error notifications. Then select the **Send an email notification when a failure occurs** check box.

	![Screenshot that shows boxes for notification email.](common/provisioning-notification-email.png)

7. Select **Save**.

8. In the **Mappings** section, select **Synchronize Azure Active Directory Users to ServiceNow**.

9. Review the user attributes that are synchronized from Azure AD to ServiceNow in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in ServiceNow for update operations. 

   If you choose to change the [matching target attribute](../app-provisioning/customize-application-attributes.md), you'll need to ensure that the ServiceNow API supports filtering users based on that attribute. 
   
   Select the **Save** button to commit any changes.

10. In the **Mappings** section, select **Synchronize Azure Active Directory Groups to ServiceNow**.

11. Review the group attributes that are synchronized from Azure AD to ServiceNow in the **Attribute-Mapping** section. The attributes selected as **Matching** properties are used to match the groups in ServiceNow for update operations. Select the **Save** button to commit any changes.

12. To configure scoping filters, see the instructions in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for ServiceNow, change **Provisioning Status** to **On** in the **Settings** section.

	![Screenshot that shows Provisioning Status switched on.](common/provisioning-toggle-on.png)

14. Define the users and groups that you want to provision to ServiceNow by choosing the desired values in **Scope** in the **Settings** section.

	![Screenshot that shows choices for provisioning scope.](common/provisioning-scope.png)

15. When you're ready to provision, select **Save**.

	![Screenshot of the button for saving a provisioning configuration.](common/provisioning-configuration-save.png)

This operation starts the initial synchronization cycle of all users and groups defined in **Scope** in the **Settings** section. The initial cycle takes longer to perform than subsequent cycles. Subsequent cycles occur about every 40 minutes, as long as the Azure AD provisioning service is running. 

## Step 6: Monitor your deployment
After you've configured provisioning, use the following resources to monitor your deployment:

- Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully.
- Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion.
- If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. [Learn more about quarantine states](../app-provisioning/application-provisioning-quarantine-status.md).  

## Troubleshooting tips
* When you're provisioning certain attributes (such as **Department** and **Location**) in ServiceNow, the values must already exist in a reference table in ServiceNow. If they don't, you'll get an **InvalidLookupReference** error. 

   For example, you might have two locations (Seattle, Los Angeles) and three departments (Sales, Finance, Marketing) in a certain table in ServiceNow. If you try to provision a user whose department is "Sales" and whose location is "Seattle," that user will be provisioned successfully. If you try to provision a user whose department is "Sales" and whose location is "LA," the user won't be provisioned. The location "LA" must be added to the reference table in ServiceNow, or the user attribute in Azure AD must be updated to match the format in ServiceNow. 
* If you get an **EntryJoiningPropertyValueIsMissing** error, review your [attribute mappings](../app-provisioning/customize-application-attributes.md) to identify the matching attribute. This value must be present on the user or group you're trying to provision. 
* To understand any requirements or limitations (for example, the format to specify a country code for a user), review the [ServiceNow SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Provisioning requests are sent by default to https://{your-instance-name}.service-now.com/{table-name}. If you need a custom tenant URL, you can provide the entire URL as the instance name.
* The **ServiceNowInstanceInvalid** error indicates a problem communicating with the ServiceNow instance. Here's the text of the error:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  If you're having test connection problems, try selecting **No** for the following settings in ServiceNow:
   
  - **System Security** > **High security settings** > **Require basic authentication for incoming SCHEMA requests**
  - **System Properties** > **Web Services** > **Require basic authorization for incoming SOAP requests**

     ![Screenshot that shows the option for authorizing SOAP requests.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  If you still can't resolve your problem, contact ServiceNow support and ask them to turn on SOAP debugging to help troubleshoot. 

* The Azure AD provisioning service currently operates under particular [IP ranges](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). If necessary, you can restrict other IP ranges and add these particular IP ranges to the allow list of your application. That technique will allow traffic flow from the Azure AD provisioning service to your application.

## Additional resources

* [Managing user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What are application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)
