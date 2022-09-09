---
title: 'Tutorial: Configure Templafy OpenID Connect for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Templafy OpenID Connect.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
---

# Tutorial: Configure Templafy OpenID Connect for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Templafy OpenID Connect and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Templafy OpenID Connect.

> [!NOTE]
> This tutorial describes a connector built on top of the Azure AD User Provisioning Service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> This connector is currently in Public Preview. For more information on the general Microsoft Azure terms of use for Preview features, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* An Azure AD tenant.
* [A Templafy tenant](https://www.templafy.com/pricing/).
* A user account in Templafy with Admin permissions.

## Step 1. Plan your provisioning deployment
1. Learn about [how the provisioning service works](../app-provisioning/user-provisioning.md).
2. Determine who will be in [scope for provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determine what data to [map between Azure AD and Templafy OpenID Connect](../app-provisioning/customize-application-attributes.md). 

## Assigning users to Templafy OpenID Connect

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Templafy OpenID Connect. Once decided, you can assign these users and/or groups to Templafy OpenID Connect by following the instructions here:
* [Assign a user or group to an enterprise app](../manage-apps/assign-user-or-group-access-portal.md)

## Important tips for assigning users to Templafy OpenID Connect

* It is recommended that a single Azure AD user is assigned to Templafy OpenID Connect to test the automatic user provisioning configuration. More users and/or groups may be assigned later.

* When assigning a user to Templafy OpenID Connect, you must select any valid application-specific role (if available) in the assignment dialog. Users with the **Default Access** role are excluded from provisioning.

## Step 2. Configure Templafy OpenID Connect to support provisioning with Azure AD

Before configuring Templafy OpenID Connect for automatic user provisioning with Azure AD, you will need to enable SCIM provisioning on Templafy OpenID Connect.

1. Sign in to your Templafy Admin Console. Click on **Administration**.

	![Templafy Admin Console](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Click on **Authentication Method**.

	![Screenshot of the Templafy administration section with the Authentication method option called out.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Copy the **SCIM Api-key** value. This value will be entered in the **Secret Token** field in the Provisioning tab of your Templafy OpenID Connect application in the Azure portal.

	![A screenshot of the S C I M A P I key.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## Step 3. Add Templafy OpenID Connect from the gallery

To configure Templafy OpenID Connect for automatic user provisioning with Azure AD, you need to add Templafy OpenID Connect from the Azure AD application gallery to your list of managed SaaS applications.

**To add Templafy OpenID Connect from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, in the left navigation panel, select **Azure Active Directory**.

	![The Azure Active Directory button](common/select-azuread.png)

2. Go to **Enterprise applications**, and then select **All applications**.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

	![The New application button](common/add-new-app.png)

4. In the search box, enter **Templafy OpenID Connect**, select **Templafy OpenID Connect** in the results panel, and then click the **Add** button to add the application.

	![Templafy OpenID Connect in the results list](common/search-new-app.png)

## Step 4. Configure automatic user provisioning to Templafy OpenID Connect 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Templafy OpenID Connect based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable OpenID connect-based single sign-on for Templafy, following the instructions provided in the [Templafy Single sign-on tutorial](templafy-tutorial.md). Single sign-on can be configured independently of automatic user provisioning, though these two features compliment each other.

### To configure automatic user provisioning for Templafy OpenID Connect in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Templafy OpenID Connect**.

	![The Templafy OpenID Connect link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot of the Provisioning Mode dropdown list with the Automatic option called out.](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://scim.templafy.com/scim` in **Tenant URL**. Input the **SCIM API-key** value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Templafy. If the connection fails, ensure your Templafy account has Admin permissions and try again.

	![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and check the checkbox - **Send an email notification when a failure occurs**.

	![Notification Email](common/provisioning-notification-email.png)

7. Click **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Templafy OpenID Connect**.

	![Templafy OpenID Connect User Mappings](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Review the user attributes that are synchronized from Azure AD to Templafy OpenID Connect in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Templafy OpenID Connect for update operations. Select the **Save** button to commit any changes.

   |Attribute|Type|Supported for filtering|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |displayName|String|
   |title|String|
   |preferredLanguage|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |addresses[type eq "work"].locality|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses[type eq "work"].region|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses[type eq "work"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Templafy**.

	![Templafy OpenID Connect Group Mappings](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Review the group attributes that are synchronized from Azure AD to Templafy OpenID Connect in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Templafy OpenID Connect for update operations. Select the **Save** button to commit any changes.

      |Attribute|Type|Supported for filtering|
      |---|---|---|
      |displayName|String|&check;|
      |members|Reference|
      |externalId|String|      

12. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for Templafy OpenID Connect, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Templafy OpenID Connect by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

15. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

	This operation starts the initial synchronization of all users and/or groups defined in **Scope** in the **Settings** section. The initial sync takes longer to perform than subsequent syncs, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Templafy OpenID Connect.

## Step 5. Monitor your deployment
Once you've configured provisioning, use the following resources to monitor your deployment:

* Use the [provisioning logs](../reports-monitoring/concept-provisioning-logs.md) to determine which users have been provisioned successfully or unsuccessfully
* Check the [progress bar](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) to see the status of the provisioning cycle and how close it is to completion
* If the provisioning configuration seems to be in an unhealthy state, the application will go into quarantine. Learn more about quarantine states [here](../app-provisioning/application-provisioning-quarantine-status.md).

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../app-provisioning/check-status-user-account-provisioning.md)