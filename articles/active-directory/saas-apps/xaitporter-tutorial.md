---
title: 'Tutorial: Azure Active Directory integration with XaitPorter | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and XaitPorter.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with XaitPorter

In this tutorial, you learn how to integrate XaitPorter with Azure Active Directory (Azure AD).
Integrating XaitPorter with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to XaitPorter.
* You can enable your users to be automatically signed-in to XaitPorter (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with XaitPorter, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/).
* XaitPorter single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* XaitPorter supports **SP** initiated SSO

## Adding XaitPorter from the gallery

To configure the integration of XaitPorter into Azure AD, you need to add XaitPorter from the gallery to your list of managed SaaS apps.

**To add XaitPorter from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **XaitPorter**, select **XaitPorter** from result panel then click **Add** button to add the application.

	 ![XaitPorter in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with XaitPorter based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in XaitPorter needs to be established.

To configure and test Azure AD single sign-on with XaitPorter, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure XaitPorter Single Sign-On](#configure-xaitporter-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create XaitPorter test user](#create-xaitporter-test-user)** - to have a counterpart of Britta Simon in XaitPorter that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with XaitPorter, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **XaitPorter** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![XaitPorter Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<subdomain>.xaitporter.com/saml/login`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<subdomain>.xaitporter.com`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [XaitPorter Client support team](https://www.xait.com/support/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

	![The Certificate download link](common/copy-metadataurl.png)

6. Provide the **IP address** or the **App Federation Metadata Url** to the [SmartRecruiters support team](https://www.smartrecruiters.com/about-us/contact-us/), so that XaitPorter can ensure that IP address is reachable from your XaitPorter instance configuring approved list at their side. 

### Configure XaitPorter Single Sign-On

1. To automate the configuration within XaitPorter, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

	![My apps extension](common/install-myappssecure-extension.png)

2. After adding extension to the browser, click on **Setup XaitPorter** will direct you to the XaitPorter application. From there, provide the admin credentials to sign into XaitPorter. The browser extension will automatically configure the application for you and automate steps 3-6.

	![Setup configuration](common/setup-sso.png)

3. If you want to setup XaitPorter manually, open a new web browser window and sign into your XaitPorter company site as an administrator and perform the following steps:

4. Click on **Admin**.

	![Configure Single Sign-On](./media/xaitporter-tutorial/user1.png)

5. Select **Manage Single Sign-On** from the **System Setup** dropdown list.

	![Configure Single Sign-On](./media/xaitporter-tutorial/user2.png)

6. In the **MANAGE SINGLE SIGN-ON** section, perform the following steps:

	![Configure Single Sign-On](./media/xaitporter-tutorial/user3.png)

	a. Select **Enable Single Sign-On Authentication**.

	b. In **Identity Provider Settings** textbox, paste **App Federation Metadata Url** which you have copied from the Azure portal and click **Fetch**.

	c. Select **Enable Autocreation of Users**.

	d. Click **OK**.

### Create an Azure AD test user 

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    a. In the **Name** field enter **BrittaSimon**.
  
    b. In the **User name** field type brittasimon@yourcompanydomain.extension. For example, BrittaSimon@contoso.com

    c. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    d. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to XaitPorter.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **XaitPorter**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **XaitPorter**.

	![The XaitPorter link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create XaitPorter test user

In this section, you create a user called Britta Simon in XaitPorter. Work with [XaitPorter Client support team](https://www.xait.com/support/) to add the users in the XaitPorter platform. Users must be created and activated before you use single sign-on.

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the XaitPorter tile in the Access Panel, you should be automatically signed in to the XaitPorter for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [What is Conditional Access in Azure Active Directory?](../conditional-access/overview.md)