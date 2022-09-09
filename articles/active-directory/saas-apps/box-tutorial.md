---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Box | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Box

In this tutorial, you'll learn how to integrate Box with Azure Active Directory (Azure AD). When you integrate Box with Azure AD, you can:

* Control in Azure AD who has access to Box.
* Enable your users to be automatically signed-in to Box with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Box single sign-on (SSO) enabled subscription.

> [!NOTE]
> This integration is also available to use from Azure AD US Government Cloud environment. You can find this application in the Azure AD US Government Cloud Application Gallery and configure it in the same way as you do from public cloud.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Box supports **SP** initiated SSO
* Box supports [**Automated** user provisioning and deprovisioning](./box-userprovisioning-tutorial.md) (recommended)
* Box supports **Just In Time** user provisioning

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Adding Box from the gallery

To configure the integration of Box into Azure AD, you need to add Box from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Box** in the search box.
1. Select **Box** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Box

Configure and test Azure AD SSO with Box using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Box.

To configure and test Azure AD SSO with Box, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Box SSO](#configure-box-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Box test user](#create-box-test-user)** - to have a counterpart of B.Simon in Box that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Box** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.account.box.com`

    b. In the **Identifier (Entity ID)** text box, type a URL:
    `box.net`

    c. In the **Reply URL** text box, type the URL:
    `https://sso.services.box.net/sp/ACS.saml2`

	> [!NOTE]
	> The Sign-on URL value is not real. Update the value with the actual Sign-On URL. Contact [Box Client support team](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. Your Box application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows an example for this. The default value of **Unique User Identifier** is **user.userprincipalname** but Box expects this to be mapped with the user's email address. For that you can use **user.mail** attribute from the list or use the appropriate attribute value based on your organization configuration.

	![image](common/default-attributes.png)


1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Box.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Box**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Box SSO

1. To automate the configuration within Box, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up Box** will direct you to the Box application. From there, provide the admin credentials to sign into Box. The browser extension will automatically configure the application for you and automate step 3.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup Box manually, in a different web browser window, sign in to your Box company site as an administrator and follow the procedure in [Set up SSO on your own](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE]
> If you are unable to configure the SSO settings for your Box account, you need to send the downloaded **Federation Metadata XML** to [Box support team](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Box test user

In this section, a user called Britta Simon is created in Box. Box supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Box, a new one is created after authentication.

> [!NOTE]
> If you need to create a user manually, contact [Box support team](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options.

* Select **Test this application** in the Azure portal. You're redirected to the Box Sign-on URL, where you can initiate the login flow.

* Go to Box Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Box tile in the My Apps, this will redirect to Box Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

### Push an Azure group to Box

You can push an Azure group to Box and sync that group. Azure pushes groups to Box via an API-level integration.

1. In **Users & Groups**, search for the group you want to assign to Box.
1. In **Provisioning**, ensure that **Synchronize Azure Active Directory Groups to Box** is selected. This setting syncs the groups that you allocated in the preceding step. It might take some time for these groups to be pushed from Azure.

> [!NOTE]
> If you need to create a user manually, contact [Box support team](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire).

## Next steps

Once you configure Box you can enforce Session Control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session Control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
