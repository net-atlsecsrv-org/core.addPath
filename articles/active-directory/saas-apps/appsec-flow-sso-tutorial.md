---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with AppSec Flow SSO | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and AppSec Flow SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with AppSec Flow SSO

In this tutorial, you'll learn how to integrate AppSec Flow SSO with Azure Active Directory (Azure AD). When you integrate AppSec Flow SSO with Azure AD, you can:

* Control in Azure AD who has access to AppSec Flow SSO.
* Enable your users to be automatically signed-in to AppSec Flow SSO with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* AppSec Flow SSO single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* AppSec Flow SSO supports **IDP** initiated SSO

## Adding AppSec Flow SSO from the gallery

To configure the integration of AppSec Flow SSO into Azure AD, you need to add AppSec Flow SSO from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **AppSec Flow SSO** in the search box.
1. Select **AppSec Flow SSO** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for AppSec Flow SSO

Configure and test Azure AD SSO with AppSec Flow SSO using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in AppSec Flow SSO.

To configure and test Azure AD SSO with AppSec Flow SSO, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure AppSec Flow SSO SSO](#configure-appsec-flow-sso-sso)** - to configure the single sign-on settings on application side.
    1. **[Create AppSec Flow SSO test user](#create-appsec-flow-sso-test-user)** - to have a counterpart of B.Simon in AppSec Flow SSO that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **AppSec Flow SSO** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, the application is pre-configured and the necessary URLs are already pre-populated with Azure. The user needs to save the configuration by clicking the **Save** button.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up AppSec Flow SSO** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to AppSec Flow SSO.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **AppSec Flow SSO**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure AppSec Flow SSO SSO

To configure single sign-on on **AppSec Flow SSO** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [AppSec Flow SSO support team](mailto:sre@convisoappsec.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create AppSec Flow SSO test user

In this section, you create a user called Britta Simon in AppSec Flow SSO. Work with [AppSec Flow SSO support team](mailto:sre@convisoappsec.com) to add the users in the AppSec Flow SSO platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options.

1. Click on Test this application in Azure portal and you should be automatically signed in to the AppSec Flow SSO for which you set up the SSO

1. You can use Microsoft Access Panel. When you click the AppSec Flow SSO tile in the Access Panel, you should be automatically signed in to the AppSec Flow SSO for which you set up the SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure AppSec Flow SSO you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).