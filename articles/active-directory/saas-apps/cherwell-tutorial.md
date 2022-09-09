---
title: 'Tutorial: Azure Active Directory integration with Cherwell | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Cherwell.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Cherwell

In this tutorial, you'll learn how to integrate Cherwell with Azure Active Directory (Azure AD). When you integrate Cherwell with Azure AD, you can:

* Control in Azure AD who has access to Cherwell.
* Enable your users to be automatically signed-in to Cherwell with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To configure Azure AD integration with Cherwell, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/).

* Cherwell single sign-on enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Cherwell supports **SP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Add Cherwell from the gallery

To configure the integration of Cherwell into Azure AD, you need to add Cherwell from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Cherwell** in the search box.
1. Select **Cherwell** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Cherwell

Configure and test Azure AD SSO with Cherwell using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Cherwell.

To configure and test Azure AD SSO with Cherwell, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    2. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Cherwell SSO](#configure-cherwell-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create Cherwell test user](#create-cherwell-test-user)** - to have a counterpart of B.Simon in Cherwell that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Cherwell** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<companyname>.cherwellondemand.com/cherwellclient`

    b. In the **Reply URL** text box, type the URL using the following pattern:
    `https://*.cherwellondemand.com`
	
	> [!NOTE]
	> The value is not real. Update the value with the actual Sign-on URL and Reply URL. Contact [Cherwell Client support team](https://cherwellsupport.com/CherwellPortal) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

6. On the **Set up Cherwell** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

### Create an Azure AD test user

In this section, you'll create a test user named B.Simon in the Azure portal.

1. In the left pane of the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. At the top of the screen, select **New user**.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter **B.Simon**.  
   1. In the **User name** field, enter `<username>@<companydomain>.<extension>`. For example: `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then make note of the value that's displayed in the **Password** box.
   1. Select **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Cherwell.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Cherwell**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Cherwell SSO

To configure single sign-on on **Cherwell** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Cherwell support team](https://cherwellsupport.com/CherwellPortal). They set this setting to have the SAML SSO connection set properly on both sides.

> [!NOTE]
> Your Cherwell support team has to do the actual SSO configuration. You will get a notification when SSO has been enabled for your subscription.

### Create Cherwell test user

To enable Azure AD users to sign in to Cherwell, they must be provisioned into Cherwell. In the case of Cherwell, the user accounts need to be created by your [Cherwell support team](https://cherwellsupport.com/CherwellPortal).

> [!NOTE]
> You can use any other Cherwell user account creation tools or APIs provided by Cherwell to provision Azure Active Directory user accounts.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Cherwell Sign-on URL where you can initiate the login flow. 

* Go to Cherwell Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Cherwell tile in the My Apps, you should be automatically signed in to the Cherwell for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Cherwell you can enforce Session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)