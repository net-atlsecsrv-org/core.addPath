---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Kendis - Azure AD Integration | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Kendis - Azure AD Integration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/04/2021
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Kendis - Azure AD Integration

In this tutorial, you'll learn how to integrate Kendis - Azure AD Integration with Azure Active Directory (Azure AD). When you integrate Kendis - Azure AD Integration with Azure AD, you can:

* Control in Azure AD who has access to Kendis - Azure AD Integration.
* Enable your users to be automatically signed-in to Kendis - Azure AD Integration with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Kendis - Azure AD Integration single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Kendis - Azure AD Integration supports **SP and IDP** initiated SSO
* Kendis - Azure AD Integration supports **Just In Time** user provisioning


## Adding Kendis - Azure AD Integration from the gallery

To configure the integration of Kendis - Azure AD Integration into Azure AD, you need to add Kendis - Azure AD Integration from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Kendis - Azure AD Integration** in the search box.
1. Select **Kendis - Azure AD Integration** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Kendis - Azure AD Integration

Configure and test Azure AD SSO with Kendis - Azure AD Integration using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Kendis - Azure AD Integration.

To configure and test Azure AD SSO with Kendis - Azure AD Integration, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Kendis-Azure AD Integration SSO](#configure-kendis-azure-ad-integration-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Kendis-Azure AD Integration test user](#create-kendis-azure-ad-integration-test-user)** - to have a counterpart of B.Simon in Kendis - Azure AD Integration that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Kendis - Azure AD Integration** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.kendis.io`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.kendis.io/login/saml`

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.kendis.io/login`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [Kendis - Azure AD Integration Client support team](mailto:support@kendis.io) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Kendis - Azure AD Integration** section, copy the appropriate URL(s) based on your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Kendis - Azure AD Integration.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Kendis - Azure AD Integration**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Kendis-Azure AD Integration SSO

1. To automate the configuration within Kendis - Azure AD Integration, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up Kendis - Azure AD Integration** will direct you to the Kendis - Azure AD Integration application. From there, provide the admin credentials to sign into Kendis - Azure AD Integration. The browser extension will automatically configure the application for you and automate steps 3-5.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup Kendis - Azure AD Integration manually, in a different web browser window, sign in to your Kendis - Azure AD Integration company site as an administrator.

4. Go to the **Settings > SAML Configurations**.

    ![settings to SAML Configurations](./media/kendis-scaling-agile-platform-tutorial/settings.png)

5. Click on **Edit** button at the bottom of the page and perform the following steps.

    ![SAML Configurations](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. Copy **Callback URL** value, paste this value into the **Reply URL** text box in the Basic SAML Configuration section in the Azure portal.

    b. In the **Identity Provider Single Sign On URL** textbox, paste the **Login URL** value which you have copied from the Azure portal.

    c.  In the **Identity Provider Issuer** textbox, paste the **Azure AD Identifier(Entity ID)** value which you have copied from the Azure portal.

    d. Open the downloaded **Certificate (Base64)** from the Azure portal into Notepad and paste the content into the **X.509 Certificate** textbox.

    e. **Select Default Group** from the list of options. 

    f. Click **Save**.

### Create Kendis-Azure AD Integration test user

In this section, a user called Britta Simon is created in Kendis - Azure AD Integration. Kendis - Azure AD Integration supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Kendis - Azure AD Integration, a new one is created after authentication.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to Kendis - Azure AD Integration Sign on URL where you can initiate the login flow.  

* Go to Kendis - Azure AD Integration Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the Kendis - Azure AD Integration for which you set up the SSO 

You can also use Microsoft My Apps to test the application in any mode. When you click the Kendis - Azure AD Integration tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the Kendis - Azure AD Integration for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure Kendis - Azure AD Integration you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).