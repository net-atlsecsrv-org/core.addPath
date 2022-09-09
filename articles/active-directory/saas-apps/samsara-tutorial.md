---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Samsara | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Samsara.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Samsara

In this tutorial, you'll learn how to integrate Samsara with Azure Active Directory (Azure AD). When you integrate Samsara with Azure AD, you can:

* Control in Azure AD who has access to Samsara.
* Enable your users to be automatically signed-in to Samsara with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Samsara single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Samsara supports **SP** and **IDP** initiated SSO
* Samsara supports **Just In Time** user provisioning

## Adding Samsara from the gallery

To configure the integration of Samsara into Azure AD, you need to add Samsara from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.

	![The Azure Active Directory button](common/select-azuread.png)
	
1. Navigate to **Enterprise Applications** and then select **All Applications**.

	![The Enterprise applications blade](common/enterprise-applications.png)

1. To add new application, select **New application**.

	![The New application button](common/add-new-app.png)

1. In the **Add from the gallery** section, type **Samsara** in the search box.

	 ![OneTrust Privacy Management Software in the results list](common/search-new-app.png)

1. Select **Samsara** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Samsara

Configure and test Azure AD SSO with Samsara using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Samsara.

To configure and test Azure AD SSO with Samsara, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Samsara SSO](#configure-samsara-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Samsara test user](#create-samsara-test-user)** - to have a counterpart of B.Simon in Samsara that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Samsara** application integration page, find the **Manage** section and select **single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

1. On the **Select a single sign-on method** page, select **SAML**.

    ![Single sign-on select mode](common/select-saml-option.png)

1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

	a. In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://cloud.samsara.com/signin/<ORGID>` for US cloud customers
    `https://cloud.eu.samsara.com/signin/<ORGID>` for EU cloud customers

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `urn:auth0:samsara-dev:samlp-orgid-<ORGID>`

	c. In the **Reply URL** text box, type a URL using the following pattern:
	`https://samsara-dev.auth0.com/login/callback?connection=samlp-orgid-<ORGID>`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign-on URL, Reply URL, and Identifier. Contact the [Samsara Client support team](mailto:support@samsara.com) to get these values, or in Samsara, go to **Settings** > **Single-Sign-On** > **New SAML Connection** to obtain the \<ORGID\>. You also can refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Samsara** section, copy the **login URL**

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Samsara.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Samsara**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Samsara SSO

To configure single sign-on on **Samsara** side, you need to send the downloaded **Certificate (Base64)** and **Login URL** from Azure portal to [Samsara support team](mailto:support@samsara.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Samsara test user

In this section, a user called B.Simon is created in Samsara. Samsara supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Samsara, a new one is created after authentication with a default role of Standard Admin (No Dash Cam Access) for Organization. The user's access can then be increased or decreased as needed in Samsara.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

1. Click on **Test this application** in Azure portal. This will redirect to Samsara Sign-on URL where you can initiate the login flow. 

2. Go to Samsara Sign-on URL directly and initiate the login flow from there.

3. You can use Microsoft Access Panel. When you click the Samsara tile in the Access Panel, this will redirect to Samsara Sign-on URL. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure Samsara you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).