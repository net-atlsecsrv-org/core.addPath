---
title: 'Tutorial: Azure Active Directory integration with InstaVR Viewer | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and InstaVR Viewer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess

ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---
# Tutorial: Azure Active Directory integration with InstaVR Viewer

In this tutorial, you learn how to integrate InstaVR Viewer with Azure Active Directory (Azure AD).
Integrating InstaVR Viewer with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to InstaVR Viewer.
* You can enable your users to be automatically signed-in to InstaVR Viewer (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with InstaVR Viewer, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* InstaVR Viewer single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* InstaVR Viewer supports **SP** initiated SSO
* InstaVR Viewer supports **Just In Time** user provisioning

## Adding InstaVR Viewer from the gallery

To configure the integration of InstaVR Viewer into Azure AD, you need to add InstaVR Viewer from the gallery to your list of managed SaaS apps.

**To add InstaVR Viewer from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **InstaVR Viewer**, select **InstaVR Viewer** from result panel then click **Add** button to add the application.

	 ![InstaVR Viewer in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with InstaVR Viewer based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in InstaVR Viewer needs to be established.

To configure and test Azure AD single sign-on with InstaVR Viewer, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure InstaVR Viewer Single Sign-On](#configure-instavr-viewer-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create InstaVR Viewer test user](#create-instavr-viewer-test-user)** - to have a counterpart of Britta Simon in InstaVR Viewer that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with InstaVR Viewer, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **InstaVR Viewer** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![InstaVR Viewer Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

	> [!NOTE]
	> There is no fixed pattern for Sign on URL. It is generated when the InstaVR Viewer customer does web packaging. It is unique for every customer and package. For getting the exact Sign on URL you need to login to your InstaVR Viewer instance and do web packaging.

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
	`https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

	> [!NOTE]
	> The Identifier value is not real. Update this value with the actual Identifier value which is explained later in this tutorial.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** and **Federation Metadata File** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadata-certificatebase64.png)

6. On the **Set up InstaVR Viewer** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure Ad Identifier

	c. Logout URL

### Configure InstaVR Viewer Single Sign-On

1. Open a new web browser window and log into your InstaVR Viewer company site as an administrator.

2. Click on **User Icon** and select **Account**.

	![InstaVR Viewer configuration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Scroll down to the **SAML Auth** and perform the following steps:

	![InstaVR Viewer configuration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

	a. In the **SSO URL** textbox, paste the **Login URL** value, which you have copied from the Azure portal.

	b. In the **Logout URL** textbox, paste the **Logout URL** value, which you have copied from the Azure portal.

	c. In the **Entity ID** textbox, paste the **Azure Ad Identifier** value, which you have copied from the Azure portal.

	d. To upload your downloaded Certificate file, click **Update**.

	e. To upload your downloaded Federation Metadata file, click **Update**.

	f. Copy the **Entity ID** value and paste into the **Identifier (Entity ID)** text box on the **Basic SAML Configuration** section in the Azure portal.

### Create an Azure AD test user

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    a. In the **Name** field enter **BrittaSimon**.
  
    b. In the **User name** field type **brittasimon\@yourcompanydomain.extension**  
    For example, BrittaSimon@contoso.com

    c. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    d. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to InstaVR Viewer.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **InstaVR Viewer**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, type and select **InstaVR Viewer**.

	![The InstaVR Viewer link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create InstaVR Viewer test user

In this section, a user called Britta Simon is created in InstaVR Viewer. InstaVR Viewer supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in InstaVR Viewer, a new one is created after authentication. If you face any problems, please contact to [InstaVR Viewer support team](mailto:contact@instavr.co).

### Test single sign-on

1. Open a new web browser window and log into your InstaVR Viewer company site as an administrator.

2. Select **Package** from the left navigation panel and select **Make package for Web**.

	![InstaVR Viewer configuration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Select **Download**.

	![InstaVR Viewer configuration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Select **Open Hosted Page** after that it will be redirected to Azure AD for login.

	![InstaVR Viewer configuration](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Enter your Azure AD credentials to successfully login to the Azure AD via SSO.

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
