---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Velpic SAML | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Velpic SAML

In this tutorial, you'll learn how to integrate Velpic SAML with Azure Active Directory (Azure AD). When you integrate Velpic SAML with Azure AD, you can:

* Control in Azure AD who has access to Velpic SAML.
* Enable your users to be automatically signed-in to Velpic SAML with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Velpic SAML single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Velpic SAML supports **SP** initiated SSO

## Adding Velpic SAML from the gallery

To configure the integration of Velpic SAML into Azure AD, you need to add Velpic SAML from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Velpic SAML** in the search box.
1. Select **Velpic SAML** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.    

## Configure and test Azure AD single sign-on for Velpic SAML

Configure and test Azure AD SSO with Velpic SAML using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Velpic SAML.

To configure and test Azure AD SSO with Velpic SAML, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Velpic SAML SSO](#configure-velpic-saml-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Velpic SAML test user](#create-velpic-saml-test-user)** - to have a counterpart of B.Simon in Velpic SAML that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Velpic SAML** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<sub-domain>.velpicsaml.net`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://auth.velpic.com/saml/v2/<entity-id>/login`

	> [!NOTE]
	> Please note that the Sign on URL will be provided by the Velpic SAML team and Identifier value will be available when you configure the SSO Plugin on Velpic SAML side. You need to copy that value from Velpic SAML application  page and paste it here.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

1. On the **Set up Velpic SAML** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Velpic SAML.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Velpic SAML**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Velpic SAML SSO

1. To automate the configuration within Velpic SAML, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Set up Velpic SAML** will direct you to the Velpic SAML application. From there, provide the admin credentials to sign into Velpic SAML. The browser extension will automatically configure the application for you and automate steps 3-8.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup Velpic SAML manually, open a new web browser window and sign into your Velpic SAML company site as an administrator and perform the following steps:

4. Click on **Manage** tab and go to **Integration** section where you need to click on **Plugins** button to create new plugin for Sign-In.

	![Plugin](./media/velpicsaml-tutorial/velpic_1.png)

5. Click on the **‘Add plugin’** button.
	
	![Plugin](./media/velpicsaml-tutorial/velpic_2.png)

6. Click on the **SAML** tile in the Add Plugin page.
	
	![Plugin](./media/velpicsaml-tutorial/velpic_3.png)

7. Enter the name of the new SAML plugin and click the **‘Add’** button.

	![Plugin](./media/velpicsaml-tutorial/velpic_4.png)

8. Enter the details as follows:

	![Plugin](./media/velpicsaml-tutorial/velpic_5.png)

	a. In the **Name** textbox, type the name of SAML plugin.

	b. In the **Issuer URL** textbox, paste the **Azure AD Identifier** you copied from the **Configure sign-on** window of the Azure portal.

	c. In the **Provider Metadata Config** upload the Metadata XML file which you downloaded from Azure portal.

	d. You can also choose to enable SAML just in time provisioning by enabling the **‘Auto create new users’** checkbox. If a user doesn’t exist in Velpic and this flag is not enabled, the login from Azure will fail. If the flag is enabled the user will automatically be provisioned into Velpic at the time of login. 

	e. Copy the **Single sign on URL** from the text box and paste it in the Azure portal.
	
	f. Click **Save**.

### Create Velpic SAML test user

This step is usually not required as the application supports just in time user provisioning. If the automatic user provisioning is not enabled then manual user creation can be done as described below.

Sign into your Velpic SAML company site as an administrator and perform following steps:
	
1. Click on Manage tab and go to Users section, then click on New button to add users.

	![add user](./media/velpicsaml-tutorial/velpic_7.png)

2. On the **“Create New User”** dialog page, perform the following steps.

	![user](./media/velpicsaml-tutorial/velpic_8.png)
	
	a. In the **First Name** textbox, type the first name of B.

	b. In the **Last Name** textbox, type the last name of Simon.

	c. In the **User Name** textbox, type the user name of B.Simon.

	d. In the **Email** textbox, type the email address of B.Simon@contoso.com account.

	e. Rest of the information is optional, you can fill it if needed.
	
	f. Click **SAVE**.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

1. When you click the Velpic SAML tile in the Access Panel, you should get login page of Velpic SAML application. You should see the **‘Log In With Azure AD’** button on the sign in page.

	![Plugin](./media/velpicsaml-tutorial/velpic_6.png)

1. Click on the **‘Log In With Azure AD’** button to log in to Velpic using your Azure AD account.

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Velpic SAML with Azure AD](https://aad.portal.azure.com/)

