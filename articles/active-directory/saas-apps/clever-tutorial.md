---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Clever | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Clever.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Clever

In this tutorial, you'll learn how to integrate Clever with Azure Active Directory (Azure AD). When you integrate Clever with Azure AD, you can:

* Control in Azure AD who has access to Clever.
* Enable your users to be automatically signed-in to Clever with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Clever single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Clever supports **SP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Adding Clever from the gallery

To configure the integration of Clever into Azure AD, you need to add Clever from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Clever** in the search box.
1. Select **Clever** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Clever

Configure and test Azure AD SSO with Clever using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Clever.

To configure and test Azure AD SSO with Clever, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Clever SSO](#configure-clever-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Clever test user](#create-clever-test-user)** - to have a counterpart of B.Simon in Clever that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Clever** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://clever.com/in/<companyname>`

    b. In the **Identifier (Entity ID)** text box, type the URL:
    `https://clever.com/oauth/saml/metadata.xml`

    c. In the **Reply URL** text box, type a URL using the following pattern:
    `https://clever.com/<companyname>`
	
	> [!NOTE]
	>  These values are not real. Update these values with the actual Sign-on URL and Reply URL. Contact [Clever Client support team](https://clever.com/about/contact/) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

	![The Certificate download link](common/copy-metadataurl.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Clever.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Clever**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Clever SSO

Follow the instructions given in the [link](https://support.clever.com/hc/articles/205889768-Single-Sign-On-SSO-Log-in-with-Office-365-Azure-) to configure single sign-on on Clever side.

### Create Clever test user

To enable Azure AD users to sign to Clever, they must be provisioned into Clever.

In case of Clever, Work with [Clever Client support team](https://clever.com/about/contact/) to add the users in the Clever platform. Users must be created and activated before you use single sign-on.

> [!NOTE]
> You can use any other Clever user account creation tools or APIs provided by Clever to provision Azure AD user accounts.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Clever Sign-on URL where you can initiate the login flow. 

* Go to Clever Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Clever tile in the My Apps, this will redirect to Clever Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Next steps

Once you configure Clever you can enforce Session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)