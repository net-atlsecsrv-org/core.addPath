---
title: 'Tutorial: Azure Active Directory integration with Sprinklr | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Sprinklr.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Sprinklr

In this tutorial, you learn how to integrate Sprinklr with Azure Active Directory (Azure AD).
Integrating Sprinklr with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Sprinklr.
* You can enable your users to be automatically signed-in to Sprinklr (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with Sprinklr, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* Sprinklr single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Sprinklr supports **SP** initiated SSO

## Adding Sprinklr from the gallery

To configure the integration of Sprinklr into Azure AD, you need to add Sprinklr from the gallery to your list of managed SaaS apps.

**To add Sprinklr from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **Sprinklr**, select **Sprinklr** from result panel then click **Add** button to add the application.

	 ![Sprinklr in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with Sprinklr based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Sprinklr needs to be established.

To configure and test Azure AD single sign-on with Sprinklr, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create Sprinklr test user](#create-sprinklr-test-user)** - to have a counterpart of Britta Simon in Sprinklr that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Sprinklr, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Sprinklr** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![Sprinklr Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<subdomain>.sprinklr.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<subdomain>.sprinklr.com`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Sprinklr Client support team](https://www.sprinklr.com/contact-us/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

6. On the **Set up Sprinklr** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure AD Identifier

	c. Logout URL

### Configure Sprinklr Single Sign-On

1. In a different web browser window, log in to your Sprinklr company site as an administrator.

1. Go to **Administration \> Settings**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Go to **Manage Partner \> Single Sign** on from the left pane.

    ![Manage Partner](./media/sprinklr-tutorial/ic782908.png "Manage Partner")

1. Click **+Add Single Sign Ons**.

    ![Screenshot shows the Add Single Sign Ons button.](./media/sprinklr-tutorial/ic782909.png "Single Sign-Ons")

1. On the **Single Sign on** page, perform the following steps:

    ![Screenshot shows the Single Sign on page where you can enter the values described.](./media/sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. In the **Name** textbox, type a name for your configuration (for example: *WAADSSOTest*).

    b. Select **Enabled**.

    c. Select **Use new SSO Certificate**.

    d. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it to the **Identity Provider Certificate** textbox.

    e. Paste the **Azure AD Identifier** value which you have copied from Azure Portal into the **Entity Id** textbox.

    f. Paste the **Login URL** value which you have copied from Azure Portal into the **Identity Provider Login URL** textbox.

    g. Paste the **Logout URL** value which you have copied from Azure Portal into the **Identity Provider Logout URL** textbox.

    h. As **SAML User ID Type**, select **Assertion contains User???s sprinklr.com username**.

    i. As **SAML User ID Location**, select **User ID is in the Name Identifier element of the Subject statement**.

    j. Click **Save**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### Create an Azure AD test user

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    a. In the **Name** field enter **BrittaSimon**.
  
    b. In the **User name** field type **brittasimon@yourcompanydomain.extension**  
    For example, BrittaSimon@contoso.com

    c. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    d. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Sprinklr.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Sprinklr**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Sprinklr**.

	![The Sprinklr link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Sprinklr test user

1. Log in to your Sprinklr company site as an administrator.

1. Go to **Administration \> Settings**.

    ![Administration](./media/sprinklr-tutorial/ic782907.png "Administration")

1. Go to **Manage Client \> Users** from the left pane.

    ![Screenshot shows the Add User button in Settings/Users.](./media/sprinklr-tutorial/ic782914.png "Settings")

1. Click **Add User**.

    ![Screenshot shows the Edit user dialog box where you can enter the values described.](./media/sprinklr-tutorial/ic782915.png "Settings")

1. On the **Edit user** dialog, perform the following steps:

    ![Edit user](./media/sprinklr-tutorial/ic782916.png "Edit user")

    a. In the **Email**, **First Name** and **Last Name** textboxes, type the information of an Azure AD user account you want to provision.

    b. Select **Password Disabled**.

    c. Select **Language**.

    d. Select **User Type**.

    e. Click **Update**.

    > [!IMPORTANT]
    > **Password Disabled** must be selected to enable a user to log in via an Identity provider. 

1. Go to **Role**, and then perform the following steps:

    ![Partner Roles](./media/sprinklr-tutorial/ic782917.png "Partner Roles")

    a. From the **Global** list, select **ALL_Permissions**.  

    b. Click **Update**.

> [!NOTE]
> You can use any other Sprinklr user account creation tools or APIs provided by Sprinklr to provision Azure AD user accounts.

### Test single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Sprinklr tile in the Access Panel, you should be automatically signed in to the Sprinklr for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [What is Conditional Access in Azure Active Directory?](../conditional-access/overview.md)