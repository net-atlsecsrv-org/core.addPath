---
title: 'Tutorial: Azure Active Directory integration with Rally Software | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes

---
# Tutorial: Azure Active Directory integration with Rally Software

In this tutorial, you learn how to integrate Rally Software with Azure Active Directory (Azure AD).
Integrating Rally Software with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Rally Software.
* You can enable your users to be automatically signed-in to Rally Software (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with Rally Software, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* Rally Software single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Rally Software supports **SP** initiated SSO

## Adding Rally Software from the gallery

To configure the integration of Rally Software into Azure AD, you need to add Rally Software from the gallery to your list of managed SaaS apps.

**To add Rally Software from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **Rally Software**, select **Rally Software** from result panel then click **Add** button to add the application.

	 ![Rally Software in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with Rally Software based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Rally Software needs to be established.

To configure and test Azure AD single sign-on with Rally Software, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure Rally Software Single Sign-On](#configure-rally-software-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create Rally Software test user](#create-rally-software-test-user)** - to have a counterpart of Britta Simon in Rally Software that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Rally Software, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Rally Software** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![Rally Software Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<tenant-name>.rally.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<tenant-name>.rally.com`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Rally Software Client support team](https://help.rallydev.com/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

6. On the **Set up Rally Software** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure AD Identifier

	c. Logout URL

### Configure Rally Software Single Sign-On

1. Sign in to your **Rally Software** tenant.

2. In the toolbar on the top, click **Setup**, and then select **Subscription**.
   
    ![Subscription](./media/rally-software-tutorial/ic769531.png "Subscription")

3. Click the **Action** button. Select **Edit Subscription** at the top right side of the toolbar.

4. On the **Subscription** dialog page, perform the following steps, and then click **Save & Close**:
   
    ![Authentication](./media/rally-software-tutorial/ic769542.png "Authentication")
   
    a. Select **Rally or SSO authentication** from Authentication dropdown.

    b. In the **Identity provider URL** textbox, paste the value of **Azure AD Identifier**, which you have copied from Azure portal. 

    c. In the **SSO Logout** textbox, paste the value of **Logout URL**, which you have copied from Azure portal.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Rally Software.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Rally Software**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Rally Software**.

	![The Rally Software link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Rally Software test user

For Azure AD users to be able to sign in, they must be provisioned to the Rally Software application using their Azure Active Directory user names.

**To configure user provisioning, perform the following steps:**

1. Sign in to your Rally Software tenant.

2. Go to **Setup \> USERS**, and then click **+ Add New**.
   
    ![Users](./media/rally-software-tutorial/ic781039.png "Users")

3. Type the name in the New User textbox, and then click **Add with Details**.

4. In the **Create User** section, perform the following steps:
   
    ![Create User](./media/rally-software-tutorial/ic781040.png "Create User")

	a. In the **User Name** textbox, type the name of user like **Brittsimon**.
   
    b. In **E-mail Address** textbox, enter the email of user like brittasimon@contoso.com.

	c. In **First Name** text box, enter the first name of user like **Britta**.

	d. In **Last Name** text box, enter the last name of user like **Simon**.

    e. Click **Save & Close**.

   >[!NOTE]
   >You can use any other Rally Software user account creation tools or APIs provided by Rally Software to provision Azure AD user accounts.

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Rally Software tile in the Access Panel, you should be automatically signed in to the Rally Software for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

