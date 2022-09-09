---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with LabLog | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and LabLog.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with LabLog

In this tutorial, you'll learn how to integrate LabLog with Azure Active Directory (Azure AD). When you integrate LabLog with Azure AD, you can:

* Control in Azure AD who has access to LabLog.
* Enable your users to be automatically signed-in to LabLog with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* LabLog single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* LabLog supports **SP** initiated SSO

* LabLog supports **Just In Time** user provisioning


## Adding LabLog from the gallery

To configure the integration of LabLog into Azure AD, you need to add LabLog from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **LabLog** in the search box.
1. Select **LabLog** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for LabLog

Configure and test Azure AD SSO with LabLog using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in LabLog.

To configure and test Azure AD SSO with LabLog, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure LabLog SSO](#configure-lablog-sso)** - to configure the single sign-on settings on application side.
    1. **[Create LabLog test user](#create-lablog-test-user)** - to have a counterpart of B.Simon in LabLog that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **LabLog** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<CUSTOMER_SUBDOMAIN>.labnotebook.app/lablog/login/sso/`

	> [!NOTE]
	> The value is not real. Update the value with the actual Sign-On URL. Contact [LabLog Client support team](mailto:support@labnotebook.app) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up LabLog** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to LabLog.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **LabLog**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure LabLog SSO

1. Login to the LabLog website as an administrator.

1. Click on **Single Sign-On** icon in the left menu.

1. Perform the below steps in the following page.

	![LabLog Configuration](./media/lablog-tutorial/single-sign-on.png)

	a. In the **Entity ID** textbox, paste the **Azure AD Identifier** value which you have copied from the Azure portal.

	b. In the **SAML SSO Login URL** textbox, paste the **Login URL** value which you have copied from the Azure portal.

	c. Open the downloaded **Certificate (Base64)** from the Azure portal into Notepad and paste the content into the **Public Certificate** textbox.

	d. Click on **SAVE**.


### Create LabLog test user

In this section, a user called Britta Simon is created in LabLog. LabLog supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in LabLog, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to LabLog Sign-on URL where you can initiate the login flow. 

* Go to LabLog Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the LabLog tile in the My Apps, this will redirect to LabLog Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure LabLog you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).