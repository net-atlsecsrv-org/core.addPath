---
title: 'Tutorial: Configure Soloinsight-CloudGate SSO for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd

ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
---

# Tutorial: Configure Soloinsight-CloudGate SSO for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Soloinsight-CloudGate SSO and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Soloinsight-CloudGate SSO.

> [!NOTE]
> This tutorial describes a connector built on top of the Azure AD User Provisioning Service. For important details on what this service does, how it works, and frequently asked questions, see [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../manage-apps/user-provisioning.md).
>
> This connector is currently in Public Preview. For more information on the general Microsoft Azure terms of use for Preview features, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Prerequisites

The scenario outlined in this tutorial assumes that you already have the following prerequisites:

* An Azure AD tenant
* [A Soloinsight-CloudGate SSO tenant](https://www.soloinsight.com/)
* A user account in Soloinsight-CloudGate SSO with Admin permissions.

## Assigning users to Soloinsight-CloudGate SSO

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Soloinsight-CloudGate SSO. Once decided, you can assign these users and/or groups to Soloinsight-CloudGate SSO by following the instructions here:
* [Assign a user or group to an enterprise app](../manage-apps/assign-user-or-group-access-portal.md)

## Important tips for assigning users to Soloinsight-CloudGate SSO

* It is recommended that a single Azure AD user is assigned to Soloinsight-CloudGate SSO to test the automatic user provisioning configuration. Additional users and/or groups may be assigned later.

* When assigning a user to Soloinsight-CloudGate SSO, you must select any valid application-specific role (if available) in the assignment dialog. Users with the **Default Access** role are excluded from provisioning.

## Set up Soloinsight-CloudGate SSO for provisioning

1. Sign in to your [Soloinsight-CloudGate SSO Admin Console](https://soloinsight.sigateway.com/login). Navigate to **Administration > System Settings**.

	![Soloinsight-CloudGate SSO Admin Console](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.	Navigate to **General**.

	![Soloinsight-CloudGate SSO Add SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.	Scroll down to the end of the page to get the **Tenant URL** and **Secret Token**. Copy the **Secret Token**. This value will be entered in the Secret Token field in the Provisioning tab of your Soloinsight-CloudGate SSO application in the Azure portal.

	![Soloinsight-CloudGate SSO Create Token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## Add Soloinsight-CloudGate SSO from the gallery

Before configuring Soloinsight-CloudGate SSO for automatic user provisioning with Azure AD, you need to add Soloinsight-CloudGate SSO from the Azure AD application gallery to your list of managed SaaS applications.

**To add Soloinsight-CloudGate SSO from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, in the left navigation panel, select **Azure Active Directory**.

	![The Azure Active Directory button](common/select-azuread.png)

2. Go to **Enterprise applications**, and then select **All applications**.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

	![The New application button](common/add-new-app.png)

4. In the search box, enter **Soloinsight-CloudGate SSO**, select **Soloinsight-CloudGate SSO** in the results panel, and then click the **Add** button to add the application.

	![Soloinsight-CloudGate SSO in the results list](common/search-new-app.png)

## Configuring automatic user provisioning to Soloinsight-CloudGate SSO 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Soloinsight-CloudGate SSO based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for Soloinsight-CloudGate SSO , following the instructions provided in the [Soloinsight-CloudGate SSO Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Single sign-on can be configured independently of automatic user provisioning, though these two features compliment each other

### To configure automatic user provisioning for Soloinsight-CloudGate SSO in Azure AD:

1. Sign in to the [Azure portal](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Soloinsight-CloudGate SSO**.

	![The Soloinsight-CloudGate SSO link in the Applications list](common/all-applications.png)

3. Select the **Provisioning** tab.

	![Provisioning tab](common/provisioning.png)

4. Set the **Provisioning Mode** to **Automatic**.

	![Provisioning tab](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://sigateway.com/scim/v2/sync/serviceproviderconfig` in **Tenant URL**. Input the **SCIM Authentication Token** value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Soloinsight-CloudGate SSO. If the connection fails, ensure your Soloinsight-CloudGate SSO account has Admin permissions and try again.

	![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. In the **Notification Email** field, enter the email address of a person or group who should receive the provisioning error notifications and check the checkbox - **Send an email notification when a failure occurs**.

	![Notification Email](common/provisioning-notification-email.png)

7. Click **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Soloinsight-CloudGate SSO**.

	![Soloinsight-CloudGate SSO User Mappings](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Review the user attributes that are synchronized from Azure AD to Soloinsight-CloudGate SSO in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Soloinsight-CloudGate SSO for update operations. Select the **Save** button to commit any changes.

	![Soloinsight-CloudGate SSO User Attributes](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Soloinsight-CloudGate SSO**.

	![Soloinsight-CloudGate SSO Group Mappings](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Review the group attributes that are synchronized from Azure AD to Soloinsight-CloudGate SSO in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Soloinsight-CloudGate SSO for update operations. Select the **Save** button to commit any changes.

	![Soloinsight-CloudGate SSO Group Attributes](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. To configure scoping filters, refer to the following instructions provided in the [Scoping filter tutorial](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for Soloinsight-CloudGate SSO, change the **Provisioning Status** to **On** in the **Settings** section.

	![Provisioning Status Toggled On](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Soloinsight-CloudGate SSO by choosing the desired values in **Scope** in the **Settings** section.

	![Provisioning Scope](common/provisioning-scope.png)

15. When you are ready to provision, click **Save**.

	![Saving Provisioning Configuration](common/provisioning-configuration-save.png)

This operation starts the initial synchronization of all users and/or groups defined in **Scope** in the **Settings** section. The initial sync takes longer to perform than subsequent syncs, which occur approximately every 40 minutes as long as the Azure AD provisioning service is running. You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Soloinsight-CloudGate SSO.

For more information on how to read the Azure AD provisioning logs, see [Reporting on automatic user account provisioning](../manage-apps/check-status-user-account-provisioning.md).

## Additional resources

* [Managing user account provisioning for Enterprise Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## Next steps

* [Learn how to review logs and get reports on provisioning activity](../manage-apps/check-status-user-account-provisioning.md)

