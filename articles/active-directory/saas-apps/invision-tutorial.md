---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with InVision | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and InVision.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with InVision

In this tutorial, you'll learn how to integrate InVision with Azure Active Directory (Azure AD). When you integrate InVision with Azure AD, you can:

* Control in Azure AD who has access to InVision.
* Enable your users to be automatically signed-in to InVision with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* InVision single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* InVision supports **SP and IDP** initiated SSO

## Adding InVision from the gallery

To configure the integration of InVision into Azure AD, you need to add InVision from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **InVision** in the search box.
1. Select **InVision** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for InVision

Configure and test Azure AD SSO with InVision using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in InVision.

To configure and test Azure AD SSO with InVision, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure InVision SSO](#configure-invision-sso)** - to configure the single sign-on settings on application side.
    1. **[Create InVision test user](#create-invision-test-user)** - to have a counterpart of B.Simon in InVision that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **InVision** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.invisionapp.com`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<SUBDOMAIN>.invisionapp.com`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [InVision Client support team](mailto:support@invisionapp.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up InVision** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to InVision.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **InVision**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure InVision SSO

1. To automate the configuration within InVision, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up InVision** will direct you to the InVision application. From there, provide the admin credentials to sign into InVision. The browser extension will automatically configure the application for you and automate steps 3-6.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup InVision manually, in a different web browser window, sign in to your InVision company site as an administrator.

1. Click on **Team** and select **Settings**.

    ![Screenshot shows the Team tab with Settings selected.](./media/invision-tutorial/config1.png)

1. Scroll down to **Single sign-on** and then click **Change**.

    ![Screenshot shows the Change button for Single sign-on.](./media/invision-tutorial/config3.png)

1. On the **Single sign-on** page, perform the following steps:

    ![Screenshot shows the Single sign-on page where you enter the values in this step.](./media/invision-tutorial/config4.png)

    a. Change **Require SSO for every member of < account name >** to **On**.

    b. In the **name** textbox, enter the name for example like `azureadsso`.

    c. Enter the Sign-on URL value in the **Sign-in URL** textbox.

    d. In the **Sign-out URL** textbox, paste the **Log out** URL value, which you have copied from the Azure portal.

    e. In the **SAML Certificate** textbox, open the downloaded **Certificate (Base64)** into Notepad, copy the content and paste it into SAML Certificate textbox.

    f. In the **Name ID Format** textbox, use `Unspecified` for the **Name ID Format**.

    g. Select **SHA-256** from the dropdown for the **HASH Algorithm**.

    h. Enter appropriate name for the **SSO Button Label**.

    i. Make **Allow Just-in-Time provisioning** On.

    j. Click **Update**.

### Create InVision test user

1. In a different web browser window, sign into InVision site as an administrator.

1. Click on **Team** and select **People**.

    ![Screenshot shows the Team tab with People selected.](./media/invision-tutorial/config2.png)

1. Click on the **+ ICON** to add new user.

    ![Screenshot shows the + icon to add a user.](./media/invision-tutorial/user1.png)

1. Enter the email address of the user and click **Next**.

    ![Screenshot shows the Invite to dialog box where you can enter addresses.](./media/invision-tutorial/user2.png)

1. Once verify the email address and then click **Invite**.

    ![Screenshot shows the Invite dialog where you can select Invite to proceed.](./media/invision-tutorial/user3.png)

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options.

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to InVision Sign on URL where you can initiate the login flow.

* Go to InVision Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the InVision for which you set up the SSO

You can also use Microsoft My Apps to test the application in any mode. When you click the InVision tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the InVision for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure InVision you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).