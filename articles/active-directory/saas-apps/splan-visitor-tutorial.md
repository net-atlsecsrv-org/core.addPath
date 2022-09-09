---
title: 'Tutorial: Integrate Azure Active Directory single sign-on (SSO) with Splan Visitor | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Splan Visitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes

---

# Tutorial: Integrate Azure Active Directory single sign-on (SSO) with Splan Visitor

In this tutorial, you'll learn how to integrate Splan Visitor with Azure Active Directory (Azure AD). When you integrate Splan Visitor with Azure AD, you can:

* Use Azure AD to control who has access to Splan Visitor.
* Enable users to be automatically signed in to Splan Visitor with their Azure AD accounts.
* Manage your accounts in one central location, the Azure portal.

## Prerequisites

To get started, you need:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* A Splan Visitor single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you'll configure and test Azure AD SSO in a test environment.

Splan Visitor supports IdP-initiated SSO.

## Add Splan Visitor from the gallery

To configure the integration of Splan Visitor into Azure AD, add Splan Visitor from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal by using a work or school account, or a personal Microsoft account.
1. On the left pane, select the **Azure Active Directory** service.
1. Go to **Enterprise Applications**, and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, enter **Splan Visitor** in the search box.
1. Select **Splan Visitor** from the results panel, and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Splan Visitor

Configure and test Azure AD SSO with Splan Visitor by using a test user named **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Splan Visitor.

To configure and test Azure AD SSO with Splan Visitor, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** to test Azure AD single sign-on with test user B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Splan Visitor SSO](#configure-splan-visitor-sso)** to configure the single sign-on settings with Splan Visitor.
    1. **[Create a Splan Visitor test user](#create-a-splan-visitor-test-user)** to have a counterpart of B.Simon in Splan Visitor that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal:

1. In the Azure portal, on the **Splan Visitor** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, select the **edit/pen** icon for **Basic SAML Configuration** to edit the settings.

   ![Screenshot highlighting the edit/pen icon for Basic SAML Configuration.](common/edit-urls.png)

1. In the **Basic SAML Configuration** section, the application is preconfigured and the necessary URLs are prepopulated with Azure. Select the **Save** button to save the configuration.

1. On the **Set up Single Sign-on with SAML** page, in the **SAML Signing Certificate** section, find **Federation Metadata XML**. Select **Download** to download the certificate and save it to your computer.

	![Screenshot highlighting the Federation Metadata XML download link.](common/metadataxml.png)

1. On the **Set up Splan Visitor** section, copy the appropriate URL or URLs based on your requirement.

	![Screenshot highlighting the configuration URLs section.](common/copy-configuration-urls.png)

### Create an Azure AD test user

In this section, you'll create a test user named B.Simon in the Azure portal.

1. On the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter **B.Simon**.  
   1. In the **User name** field, enter your username in _username@companydomain.extension_ format. For example, enter **B.Simon@contoso.com**.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Select **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Splan Visitor.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Splan Visitor** to open the app overview.
1. Find the **Manage** section, and then select **Users and groups**.
1. Select **Add user**, and then select **Users and groups** in the **Add Assignment** dialog box.
1. In the **Users and groups** dialog box, select **B.Simon** from the **Users** list, and then click **Select** at the bottom of the screen.
1. If the user will be assigned a role, select it from the **Select a role** drop-down menu. If no role has been set up for this app, leave the **Default Access** role selected.
1. In the **Add Assignment** dialog box, select **Assign**.

## Configure Splan Visitor SSO

To configure single sign-on with Splan Visitor, send the **Federation Metadata XML** that you downloaded and appropriate copied URLs from the Azure portal to the [Splan Visitor support team](mailto:support@splan.com). This ensures that the SAML SSO connection is set properly on both sides.

### Create a Splan Visitor test user

Create a test user named **Britta Simon** in Splan Visitor. Work with the [Splan Visitor support team](mailto:support@splan.com) to add the user to Splan Visitor. You must create and activate the user before you use single sign-on.

## Test SSO

Test your Azure AD single sign-on configuration with one of the following options:

* **Azure portal**: Select **Test this application** to automatically sign in to the Splan Visitor for which you set up SSO.
* **Microsoft My Apps portal**: Select the **Splan Visitor** tile to automatically sign in to the Splan Visitor for which you set up SSO. For more information about the My Apps portal, see [Sign in and start apps from the My Apps portal](../user-help/my-apps-portal-end-user-access.md).

## Next steps

After you configure Splan Visitor, you can [learn how to enforce session controls in Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app). Session controls help protect exfiltration and infiltration of your organization's sensitive data in real time. Session controls extend from Conditional Access.