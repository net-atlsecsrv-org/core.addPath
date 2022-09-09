---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Litmos | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Litmos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Litmos

In this tutorial, you'll learn how to integrate Litmos with Azure Active Directory (Azure AD). When you integrate Litmos with Azure AD, you can:

* Control in Azure AD who has access to Litmos.
* Enable your users to be automatically signed-in to Litmos with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Litmos single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Litmos supports **IDP** initiated SSO
* Litmos supports **Just In Time** user provisioning

## Adding Litmos from the gallery

To configure the integration of Litmos into Azure AD, you need to add Litmos from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Litmos** in the search box.
1. Select **Litmos** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for Litmos

Configure and test Azure AD SSO with Litmos using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Litmos.

To configure and test Azure AD SSO with Litmos, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Litmos SSO](#configure-litmos-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Litmos test user](#create-litmos-test-user)** - to have a counterpart of B.Simon in Litmos that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Litmos** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Set up single sign-on with SAML** page, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<companyname>.litmos.com/account/Login`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<companyname>.litmos.com/integration/samllogin`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier and Reply URL, which are explained later in tutorial or contact [Litmos Client support team](https://www.litmos.com/contact-us) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Litmos** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Litmos.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Litmos**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Litmos SSO

1. In a different browser window, sign-on to your Litmos company site as administrator.

2. In the navigation bar on the left side, click **Accounts**.

    ![Accounts Section on App Side][22]

3. Click the **Integrations** tab.

    ![Integration Tab][23]

4. On the **Integrations** tab, scroll down to **3rd Party Integrations**, and then click **SAML 2.0** tab.

    ![SAML 2.0 Section][24]

5. Copy the value under **The SAML endpoint for litmos is:** and paste it into the **Reply URL** textbox in the **Litmos Domain and URLs** section in Azure portal.

    ![SAML endpoint][26]

6. In your **Litmos** application, perform the following steps:

    ![Litmos Application][25]

	a. Click **Enable SAML**.

	b. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it to the **SAML X.509 Certificate** textbox.

	c. Click **Save Changes**.

### Create Litmos test user

The objective of this section is to create a user called Britta Simon in Litmos. The Litmos application supports Just-in-Time provisioning. This means, a user account is automatically created if necessary during an attempt to access the application using the Access Panel.

**To create a user called Britta Simon in Litmos, perform the following steps:**

1. In a different browser window, sign-on to your Litmos company site as administrator.

2. In the navigation bar on the left side, click **Accounts**.

    ![Accounts Section On App Side][22]

3. Click the **Integrations** tab.

    ![Integrations Tab][23]

4. On the **Integrations** tab, scroll down to **3rd Party Integrations**, and then click **SAML 2.0** tab.

    ![SAML 2.0][24]

5. Select **Autogenerate Users**
  
    ![Autogenerate Users][27]

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Litmos tile in the Access Panel, you should be automatically signed in to the Litmos for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [What is conditional access in Azure Active Directory?](../conditional-access/overview.md)

- [Try Litmos with Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png