---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Zoom | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Zoom

In this tutorial, you'll learn how to integrate Zoom with Azure Active Directory (Azure AD). When you integrate Zoom with Azure AD, you can:

* Control in Azure AD who has access to Zoom.
* Enable your users to be automatically signed-in to Zoom with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Zoom single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Zoom supports **SP** initiated SSO and 
* Zoom supports [**Automated** user provisioning](./zoom-provisioning-tutorial.md).

## Adding Zoom from the gallery

To configure the integration of Zoom into Azure AD, you need to add Zoom from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Zoom** in the search box.
1. Select **Zoom** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Zoom

Configure and test Azure AD SSO with Zoom using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Zoom.

To configure and test Azure AD SSO with Zoom, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
	1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
	1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Zoom SSO](#configure-zoom-sso)** - to configure the Single Sign-On settings on application side.
	1. **[Create Zoom test user](#create-zoom-test-user)** - to have a counterpart of B.Simon in Zoom that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Zoom** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<companyname>.zoom.us`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `<companyname>.zoom.us`

    c. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<companyname>.zoom.us`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Zoom Client support team](https://support.zoom.us/hc/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Zoom** section, copy the appropriate URL(s) based on your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

> [!NOTE]
> To learn how to configure Role in Azure AD, see [Configure the role claim issued in the SAML token for enterprise applications](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> Zoom might expect a group claim in the SAML payload. If you have created any groups, contact the [Zoom Client support team](https://support.zoom.us/hc/) with the group information so they can configure the group information on their end. You also need to provide the Object ID to [Zoom Client support team](https://support.zoom.us/hc/) so they can configure the Object ID on their end. To get the Object ID, see [Configuring Zoom with Azure](https://support.zoom.us/hc/articles/115005887566).

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Zoom.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Zoom**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Zoom SSO

1. To automate the configuration within Zoom, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up Zoom** will direct you to the Zoom application. From there, provide the admin credentials to sign into Zoom. The browser extension will automatically configure the application for you and automate steps 3-6.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup Zoom manually, in a different web browser window, sign in to your Zoom company site as an administrator.

2. Click the **Single Sign-On** tab.

    ![Single sign-on tab](./media/zoom-tutorial/zoom-sso1.png "Single sign-on")

3. Click the **Security Control** tab, and then go to the **Single Sign-On** settings.

4. In the Single Sign-On section, perform the following steps:

    ![Single sign-on section](./media/zoom-tutorial/zoom-sso2.png "Single sign-on")

    a. In the **Sign-in page URL** textbox, paste the value of **Login URL** which you have copied from Azure portal.

    b. For **Sign-out page URL** value, you need to go to the Azure portal and click on **Azure Active Directory** on the left then navigate to **App registrations**.

	![The Azure Active Directory button](./media/zoom-tutorial/appreg.png)

	c. Click on **Endpoints**

	![The End point button](./media/zoom-tutorial/endpoint.png)

	d. Copy the **SAML-P SIGN-OUT ENDPOINT** and paste it into **Sign-out page URL** textbox.

	![The Copy End point button](./media/zoom-tutorial/endpoint1.png)

    e. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it to the **Identity provider certificate** textbox.

    f. In the **Issuer** textbox, paste the value of **Azure AD Identifier** which you have copied from Azure portal. 

    g. Click **Save Changes**.

    > [!NOTE]
	> For more information, visit the zoom documentation [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### Create Zoom test user

The objective of this section is to create a user called B.Simon in Zoom. Zoom supports automatic user provisioning, which is by default enabled. You can find more details [here](./zoom-provisioning-tutorial.md) on how to configure automatic user provisioning.

> [!NOTE]
> If you need to create a user manually, you need to contact [Zoom Client support team](https://support.zoom.us/hc/)

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options.

* Click on **Test this application** in Azure portal. This will redirect to Zoom Sign-on URL where you can initiate the login flow.

* Go to Zoom Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Zoom tile in the My Apps, this will redirect to Zoom Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Next steps

Once you configure Azure AD Zoom you can enforce Session Control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session Control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
