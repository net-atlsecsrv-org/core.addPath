---
title: 'Tutorial: Azure Active Directory integration with Aha! | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
---

# Tutorial: Integrate Aha! with Azure Active Directory

In this tutorial, you'll learn how to integrate Aha! with Azure Active Directory (Azure AD). When you integrate Aha! with Azure AD, you can:

* Control in Azure AD who has access to Aha!.
* Enable your users to be automatically signed-in to Aha! with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Aha! single sign-on (SSO) enabled subscription.

> [!NOTE]
> This integration is also available to use from Azure AD US Government Cloud environment. You can find this application in the Azure AD US Government Cloud Application Gallery and configure it in the same way as you do from public cloud.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Aha! supports **SP** initiated SSO
* Aha! supports **Just In Time** user provisioning

## Add Aha! from the gallery

To configure the integration of Aha! into Azure AD, you need to add Aha! from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Aha!** in the search box.
1. Select **Aha!** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Aha!

Configure and test Azure AD SSO with Aha! using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Aha!.

To configure and test Azure AD SSO with Aha!, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Aha! SSO](#configure-aha-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create Aha! test user](#create-aha-test-user)** - to have a counterpart of B.Simon in Aha! that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Aha!** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

    ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<companyname>.aha.io/session/new`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<companyname>.aha.io`

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Aha! Client support team](https://www.aha.io/company/contact) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/metadataxml.png)

6. On the **Set up Aha!** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Aha!.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Aha!**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Aha! SSO

1. To automate the configuration within Aha!, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

    ![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Setup Aha!** will direct you to the Aha! application. From there, provide the admin credentials to sign into Aha!. The browser extension will automatically configure the application for you and automate steps 3-8.

    ![Setup configuration](common/setup-sso.png)

3. If you want to setup Aha! manually, open a new web browser window and sign into your Aha! company site as an administrator and perform the following steps:

4. In the menu on the top, click **Settings**.

    ![Settings](./media/aha-tutorial/setting.png "Settings")

5. Click **Account**.

    ![Profile](./media/aha-tutorial/account.png "Profile")

6. Click **Security and single sign-on**.

    ![Screenshot that highlights the Security and single sign-on menu option.](./media/aha-tutorial/security.png "Security and single sign-on")

7. In **Single Sign-On** section, as **Identity Provider**, select **SAML2.0**.

    ![Security and single sign-on](./media/aha-tutorial/saml.png "Security and single sign-on")

8. On the **Single Sign-On** configuration page, perform the following steps:

    ![Single Sign-On](./media/aha-tutorial/sso.png "Single Sign-On")

    a. In the **Name** textbox, type a name for your configuration.

    b. For **Configure using**, select **Metadata File**.

    c. To upload your downloaded metadata file, click **Browse**.

    d. Click **Update**.

### Create Aha! test user

In this section, a user called B.Simon is created in Aha!. Aha! supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Aha!, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Aha! Sign-on URL where you can initiate the login flow. 

* Go to Aha! Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Aha! tile in the My Apps, this will redirect to Aha! Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Aha! you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).