---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Nuclino | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Nuclino.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Nuclino

In this tutorial, you'll learn how to integrate Nuclino with Azure Active Directory (Azure AD). When you integrate Nuclino with Azure AD, you can:

* Control in Azure AD who has access to Nuclino.
* Enable your users to be automatically signed-in to Nuclino with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Nuclino single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Nuclino supports **SP and IDP** initiated SSO
* Nuclino supports **Just In Time** user provisioning

## Adding Nuclino from the gallery

To configure the integration of Nuclino into Azure AD, you need to add Nuclino from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Nuclino** in the search box.
1. Select **Nuclino** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for Nuclino

Configure and test Azure AD SSO with Nuclino using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Nuclino.

To configure and test Azure AD SSO with Nuclino, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Nuclino SSO](#configure-nuclino-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Nuclino test user](#create-nuclino-test-user)** - to have a counterpart of B.Simon in Nuclino that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Nuclino** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier and Reply URL from the **Authentication** section, which is explained later in this tutorial.

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://app.nuclino.com/<UNIQUE-ID>/login`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [Nuclino Client support team](mailto:contact@nuclino.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

6. Nuclino application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![image](common/edit-attribute.png)

7. In addition to above, Nuclino application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.

	| Name |  Source Attribute|
	| ---------------| --------- |
	| first_name | user.givenname |
	| last_name | user.surname |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Nuclino** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Nuclino.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Nuclino**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Nuclino SSO

1. To automate the configuration within Nuclino, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up Nuclino** will direct you to the Nuclino application. From there, provide the admin credentials to sign into Nuclino. The browser extension will automatically configure the application for you and automate steps 3-7.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup Nuclino manually, open a new web browser window and sign into your Nuclino company site as an administrator and perform the following steps:

4. Click on the **ICON**.

	![Nuclino Configuration](./media/nuclino-tutorial/configure1.png)

5. Click on the **Azure AD SSO** and select **Team settings** from the dropdown.

	![Nuclino Configuration](./media/nuclino-tutorial/configure2.png)

6. Select **Authentication** from left navigation pane.

	![Nuclino Configuration](./media/nuclino-tutorial/configure3.png)

7. In the **Authentication** section, perform the following steps:

	![Nuclino Configuration](./media/nuclino-tutorial/configure4.png)

	a. Select **SAML-based single sign-on (SSO)**.

	b. Copy **ACS URL (You need to copy and paste this to your SSO provider)** value and paste it into the **Reply URL** textbox of the **Basic SAML Configuration** section in the Azure portal.

	c. Copy **Entity ID (You need to copy and paste this to your SSO provider)** value and paste it into the **Identifier** textbox of the **Basic SAML Configuration** section in the Azure portal.

	d. In the **SSO URL** textbox, paste the **Login URL** value which you have copied from the Azure portal.

	e. In the **Entity ID** textbox, paste the **Azure AD Identifier** value which you have copied from the Azure portal.

	f. Open your downloaded **Certificate(Base64)** file in Notepad. Copy the content of it into your clipboard, and then paste it to the **Public certificate** text box.

	g. Click **SAVE CHANGES**.

### Create Nuclino test user

In this section, a user called B.Simon is created in Nuclino. Nuclino supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Nuclino, a new one is created after authentication.

> [!Note]
> If you need to create a user manually, contact [Nuclino support team](mailto:contact@nuclino.com).

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Nuclino tile in the Access Panel, you should be automatically signed in to the Nuclino for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Nuclino with Azure AD](https://aad.portal.azure.com/)

