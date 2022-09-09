---
title: 'Tutorial: Configure MindTickle for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to MindTickle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
---

# Tutorial: Configure MindTickle for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in MindTickle and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to MindTickle.

> [!NOTE]
> This tutorial describes a connector built on top of the Azure AD User Provisioning Service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> This connector is currently in Public Preview. For more information on the general Microsoft Azure terms of use for Preview features, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* An Azure AD tenant
* [A MindTickle tenant](https://www.mindtickle.com/)
* A user account in MindTickle with Admin permissions.

## Assigning users to MindTickle

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to MindTickle. Once decided, you can assign these users and/or groups to MindTickle by following the instructions here:
* [Assign a user or group to an enterprise app](../manage-apps/assign-user-or-group-access-portal.md)

## Important tips for assigning users to MindTickle

* It is recommended that a single Azure AD user is assigned to MindTickle to test the automatic user provisioning configuration. Additional users and/or groups may be assigned later.

* When assigning a user to MindTickle, you must select any valid application-specific role (if available) in the assignment dialog. Users with the **Default Access** role are excluded from provisioning.

## Setup MindTickle for provisioning

Before configuring MindTickle for automatic user provisioning with Azure AD, you will need to enable SCIM provisioning on MindTickle.


1.	Reach out to the  [MindTickle's support team](mailto:help@mindtickle.com) to obtain the JWT token needed to configure SCIM provisioning.


## Add MindTickle from the gallery

To configure MindTickle for automatic user provisioning with Azure AD, you need to add MindTickle from the Azure AD application gallery to your list of managed SaaS applications.

**To add MindTickle from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, in the left navigation panel, select **Azure Active Directory**.

	![The Azure Active Directory button](common/select-azuread.png)

2. Go to **Enterprise applications**, and then select **All applications**.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

	![The New application button](common/add-new-app.png)

4. In the search box, enter **MindTickle**, select **MindTickle** in the results panel, and then click the **Add** button to add the application.

	![MindTickle in the results list](common/search-new-app.png)

## Configuring automatic user provisioning to MindTickle 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in MindTickle based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for MindTickle , following the instructions provided in the [MindTickle Single sign-on tutorial](mindtickle-tutorial.md). Single sign-on can be configured independently of automatic user provisioning, though these two features compliment each other

### To configure automatic user provisioning for MindTickle in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **MindTickle**.

	![The MindTickle link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Screenshot of the Manage options with the Provisioning option called out.](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Screenshot of the Provisioning Mode dropdown list with the Automatic option called out.](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://admin.mindtickle.com/scim` in **Tenant URL**. Input the **JWT token** value retrieved earlier In Secret Token textbox, enter the **JWT token** value which was given by MindTickle support team. Click **Test Connection** to ensure Azure AD can connect to myPolicies. If the connection fails, ensure your MindTickle account has Admin permissions and try again.

	![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and check the checkbox - **Send an email notification when a failure occurs**.

	![Notification Email](common/provisioning-notification-email.png)

7. Click **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to MindTickle**.

	:::image type="content" source="media/mindtickle-provisioning-tutorial/usermapping.png" alt-text="Screenshot of the Mappings section. Under Name, Synchronize Azure Active Directory Users to MindTickle is visible." border="false":::

9. Review the user attributes that are synchronized from Azure AD to MindTickle in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in MindTickle for update operations. Select the **Save** button to commit any changes.

	:::image type="content" source="media/mindtickle-provisioning-tutorial/userattribute.png" alt-text="Screenshot of the Attribute Mappings page. A table lists Azure Active Directory and MindTickle attributes and the matching precedence." border="false":::

12. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for MindTickle, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to MindTickle by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

15. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization of all users and/or groups defined in **Scope** in the **Settings** section. The initial sync takes longer to perform than subsequent syncs. For more information on how long it will take for users and/or groups to provision, see [How long will it take to provision users](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

You can use the **Current Status** section to monitor progress and follow links to your provisioning activity report, which describes all actions performed by the Azure AD provisioning service on MindTickle. For more information, see [Check the status of user provisioning](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). To read the Azure AD provisioning logs, see [Reporting on automatic user account provisioning](../app-provisioning/check-status-user-account-provisioning.md).

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
