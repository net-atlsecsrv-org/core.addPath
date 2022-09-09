---
title: 'Tutorial: Azure Active Directory integration with ExpenseIn | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and ExpenseIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
---

# Tutorial: Integrate ExpenseIn with Azure Active Directory

In this tutorial, you'll learn how to integrate ExpenseIn with Azure Active Directory (Azure AD). When you integrate ExpenseIn with Azure AD, you can:

* Control in Azure AD who has access to ExpenseIn.
* Enable your users to be automatically signed-in to ExpenseIn with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* ExpenseIn single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment. 
* ExpenseIn supports **SP and IDP** initiated SSO.
* Once you configure ExpenseIn you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## Adding ExpenseIn from the gallery

To configure the integration of ExpenseIn into Azure AD, you need to add ExpenseIn from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **ExpenseIn** in the search box.
1. Select **ExpenseIn** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for ExpenseIn

Configure and test Azure AD SSO with ExpenseIn using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in ExpenseIn.

To configure and test Azure AD SSO with ExpenseIn, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** to enable B.Simon to use Azure AD single sign-on.
1. **[Configure ExpenseIn SSO](#configure-expensein-sso)** to configure the SSO settings on application side.
    1. **[Create ExpenseIn test user](#create-expensein-test-user)** to have a counterpart of B.Simon in ExpenseIn that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **ExpenseIn** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, the user does not have to perform any steps as the app is already pre-integrated with Azure.

5. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL:
    `https://app.expensein.com/saml`

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and click **Download** to download the **Certificate (Base64)** and save it on your computer.

   ![The Certificate download link](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. On the **Set up ExpenseIn** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to ExpenseIn.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **ExpenseIn**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.


## Configure ExpenseIn SSO

1. To automate the configuration within ExpenseIn, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

1. After adding extension to the browser, click on **Set up ExpenseIn** will direct you to the ExpenseIn application. From there, provide the admin credentials to sign into ExpenseIn. The browser extension will automatically configure the application for you and automate steps 3-5.

	![Setup configuration](common/setup-sso.png)

1. If you want to setup ExpenseIn manually, log in to your ExpenseIn company site as an administrator.

1. Click on **Admin** on the top of the page then navigate to **Single Sign-On** and click **Add provider**.

	 ![Screenshot that shows the "Admin" tab and the "Single Sign-On - Providers" page and "Add Provider" selected.](./media/expenseIn-tutorial/config01.png)

1. On the **New Identity Provider** pop-up, Perform the following steps:

    ![Screenshot that shows the "Edit Identity Provider" pop-up with values entered.](./media/expenseIn-tutorial/config02.png)

	a. In the **Provider Name** text box, type the name; for example, Azure.

	b. Select **Yes** for **Allow Provider Intitiated Sign-On**.

	c. In the **Target Url** text box, paste the value of **Login URL**, which you have copied from Azure portal.

    d. In the **Issuer** text box, paste the value of **Azure AD Identifier**, which you have copied from Azure portal.

    e. Open the Certificate (Base64) in Notepad, copy its content and paste it in the **Certificate** text box.

	f. Click **Create**.

### Create ExpenseIn test user

To enable Azure AD users to sign in to ExpenseIn, they must be provisioned into ExpenseIn. In ExpenseIn, provisioning is a manual task.

**To provision a user account, perform the following steps:**

1. Sign in to ExpenseIn as an Administrator.

2. Click on **Admin** on the top of the page then navigate to **Users** and click **New User**.

	 ![Screenshot that shows the "Admin" tab and the "Manage Users" page with "New User" selected.](./media/expenseIn-tutorial/config03.png)

3. On the **Details** pop-up, perform the following steps:

    ![ExpenseIn configuration](./media/expenseIn-tutorial/config04.png)

    a. In **First Name** text box, enter the first name of user like **B**.

    b. In **Last Name** text box, enter the last name of user like **Simon**.

    c. In **Email** text box, enter the email of user like `B.Simon@contoso.com`.

    d. Click **Create**.

## Test SSO

When you select the ExpenseIn tile in the Access Panel, you should be automatically signed in to the ExpenseIn for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try ExpenseIn with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [How to protect ExpenseIn with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
