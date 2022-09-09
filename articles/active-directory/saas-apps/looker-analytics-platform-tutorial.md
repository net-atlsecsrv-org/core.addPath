---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Looker Analytics Platform | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Looker Analytics Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Looker Analytics Platform

In this tutorial, you'll learn how to integrate Looker Analytics Platform with Azure Active Directory (Azure AD). When you integrate Looker Analytics Platform with Azure AD, you can:

* Control in Azure AD who has access to Looker Analytics Platform.
* Enable your users to be automatically signed-in to Looker Analytics Platform with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Looker Analytics Platform single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Looker Analytics Platform supports **SP and IDP** initiated SSO
* Looker Analytics Platform supports **Just In Time** user provisioning


## Adding Looker Analytics Platform from the gallery

To configure the integration of Looker Analytics Platform into Azure AD, you need to add Looker Analytics Platform from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Looker Analytics Platform** in the search box.
1. Select **Looker Analytics Platform** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Looker Analytics Platform

Configure and test Azure AD SSO with Looker Analytics Platform using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Looker Analytics Platform.

To configure and test Azure AD SSO with Looker Analytics Platform, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Looker Analytics Platform SSO](#configure-looker-analytics-platform-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Looker Analytics Platform test user](#create-looker-analytics-platform-test-user)** - to have a counterpart of B.Simon in Looker Analytics Platform that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Looker Analytics Platform** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `<SPN>_looker`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.looker.com`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [Looker Analytics Platform Client support team](mailto:support@looker.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

1. On the **Set up Looker Analytics Platform** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Looker Analytics Platform.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Looker Analytics Platform**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Looker Analytics Platform SSO

1. In a different web browser window, sign into Looker Analytics Platform website as an administrator.

1. Go to the **Admin** -> **Authentication** -> **SAML**

    ![screenshot for SAML option](./media/looker-analytics-platform-tutorial/admin.png)

1. Paste the **Federation Metadata** information that you copied from the Azure portal in to the **IDP Metadata** textbox and click on **Load**.

    ![screenshot for metadata upload](./media/looker-analytics-platform-tutorial/metadata.png)

1. Perform the following steps in the **User Attribute Settings** section.

    ![screenshot for User Attribute Settings](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Add the following value in the Email Attr field: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Add the following value to the Fname Attr field: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Add the following value to the Lname Attr field: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. In the **Test User Authentication**, click on **Test SAML Authentication**. If the page that loads say “Server Response Successfully Validated”, you have successfully set up the instance for SAML integration.
    
    e. In **Save and Apply Settings**, Check the box **I have confirmed the configuration above and want to enable applying it globally**.

    f. Click the **Update Settings** button.

### Create Looker Analytics Platform test user

In this section, a user called Britta Simon is created in Looker Analytics Platform. Looker Analytics Platform supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Looker Analytics Platform, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to Looker Analytics Platform Sign-on URL where you can initiate the login flow.  

* Go to Looker Analytics Platform Sign on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the Looker Analytics Platform for which you set up the SSO 

You can also use Microsoft Access Panel to test the application in any mode. When you click the Looker Analytics Platform tile in the Access Panel, if configured in SP mode you would be redirected to the application sign-on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the Looker Analytics Platform for which you set up the SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Looker Analytics Platform you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).