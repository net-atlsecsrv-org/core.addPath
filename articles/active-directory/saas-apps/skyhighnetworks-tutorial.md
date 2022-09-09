---
title: 'Tutorial: Azure Active Directory integration with MVISION Cloud Azure AD SSO Configuration | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and MVISION Cloud Azure AD SSO Configuration.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
---
# Tutorial: Integrate MVISION Cloud Azure AD SSO Configuration with Azure Active Directory

In this tutorial, you'll learn how to integrate MVISION Cloud Azure AD SSO Configuration with Azure Active Directory (Azure AD). When you integrate MVISION Cloud Azure AD SSO Configuration with Azure AD, you can:

* Control in Azure AD who has access to MVISION Cloud Azure AD SSO Configuration.
* Enable your users to be automatically signed-in to MVISION Cloud Azure AD SSO Configuration with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* MVISION Cloud Azure AD SSO Configuration single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* MVISION Cloud Azure AD SSO Configuration supports **SP and IDP** initiated SSO.

## Add MVISION Cloud Azure AD SSO Configuration from the gallery

To configure the integration of MVISION Cloud Azure AD SSO Configuration into Azure AD, you need to add MVISION Cloud Azure AD SSO Configuration from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **MVISION Cloud Azure AD SSO Configuration** in the search box.
1. Select **MVISION Cloud Azure AD SSO Configuration** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for MVISION Cloud Azure AD SSO Configuration

Configure and test Azure AD SSO with MVISION Cloud Azure AD SSO Configuration using a test user called **Britta Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in MVISION Cloud Azure AD SSO Configuration.

To configure and test Azure AD SSO with MVISION Cloud Azure AD SSO Configuration, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
    4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
1. **[Configure MVISION Cloud Azure AD SSO Configuration SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create MVISION Cloud Azure AD SSO Configuration test user](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** - to have a counterpart of Britta Simon in MVISION Cloud Azure AD SSO Configuration that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Datadog** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, If you wish to configure the application in **IDP** initiated mode, perform the following steps:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [MVISION Cloud Azure AD SSO Configuration Client support team](mailto:support@skyhighnetworks.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

6. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

7. On the **Set up MVISION Cloud Azure AD SSO Configuration** section, copy the appropriate URL(s) as per your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to MVISION Cloud Azure AD SSO Configuration.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **MVISION Cloud Azure AD SSO Configuration**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure MVISION Cloud Azure AD SSO Configuration SSO

To configure single sign-on on **MVISION Cloud Azure AD SSO Configuration** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [MVISION Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create MVISION Cloud Azure AD SSO Configuration test user

In this section, you create a user called B.Simon in MVISION Cloud Azure AD SSO Configuration. Work with [MVISION Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com) to add the users in the MVISION Cloud Azure AD SSO Configuration platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to MVISION Cloud Azure AD SSO Configuration Sign on URL where you can initiate the login flow.  

* Go to MVISION Cloud Azure AD SSO Configuration Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the MVISION Cloud Azure AD SSO Configuration for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the MVISION Cloud Azure AD SSO Configuration tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the MVISION Cloud Azure AD SSO Configuration for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure MVISION Cloud Azure AD SSO Configuration you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).