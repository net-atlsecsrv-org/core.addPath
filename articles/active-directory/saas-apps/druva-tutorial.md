---
title: 'Tutorial: Azure Active Directory integration with Druva | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---
# Tutorial: Azure Active Directory integration with Druva

In this tutorial, you learn how to integrate Druva with Azure Active Directory (Azure AD).
Integrating Druva with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Druva.
* You can enable your users to be automatically signed-in to Druva (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with Druva, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* Druva single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Druva supports **SP** and **IDP** initiated SSO

## Adding Druva from the gallery

To configure the integration of Druva into Azure AD, you need to add Druva from the gallery to your list of managed SaaS apps.

**To add Druva from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **Druva**, select **Druva** from result panel then click **Add** button to add the application.

	 ![Druva in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with Druva based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Druva needs to be established.

To configure and test Azure AD single sign-on with Druva, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure Druva Single Sign-On](#configure-druva-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create Druva test user](#create-druva-test-user)** - to have a counterpart of Britta Simon in Druva that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Druva, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Druva** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, If you wish to configure the application in **IDP** initiated mode, perform the following step:

    ![Druva Domain and URLs single sign-on information](common/idp-identifier.png)

    In the **Identifier** text box, type a string value:
    `druva-cloud`

5. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    ![image](common/both-preintegrated-signon.png)

    In the **Sign-on URL** text box, type a URL:
    `https://cloud.druva.com/home`

6. Druva application expects the SAML assertions in a specific format. Configure the following claims for this application. You can manage the values of these attributes from the **User Attributes** section on application integration page. On the **Set up Single Sign-On with SAML** page, click **Edit** button to open **User Attributes** dialog.

	![image](common/edit-attribute.png)

7. In the **User Claims** section on the **User Attributes** dialog, edit the claims by using **Edit icon** or add the claims by using **Add new claim** to configure SAML token attribute as shown in the image above and perform the following steps: 

	| Name | Source Attribute|
	| ------------------- | -------------------- |
	| insync\_auth\_token |Enter the token generated value |

	a. Click **Add new claim** to open the **Manage user claims** dialog.

	![image](common/new-save-attribute.png)

	![image](common/new-attribute-details.png)

	b. In the **Name** textbox, type the attribute name shown for that row.

	c. Leave the **Namespace** blank.

	d. Select Source as **Attribute**.

	e. From the **Source attribute** list, type the attribute value shown for that row.

	f. Click **Ok**

	g. Click **Save**.

8. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Certificate (Base64)** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

9. On the **Set up Druva** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure Ad Identifier

	c. Logout URL

### Configure Druva Single Sign-On

1. In a different web browser window, log in to your Druva company site as an administrator.

2. Go to **Manage \> Settings**.

	![Settings](./media/druva-tutorial/ic795091.png "Settings")

3. On the Single Sign-On Settings dialog, perform the following steps:

	![Single Sign-On Settings](./media/druva-tutorial/ic795092.png "Single Sign-On Settings")
	
	a. In **ID Provider Login URL** textbox, paste the value of **Login URL**, which you have copied from Azure portal.
		
	b. In **ID Provider Logout URL** textbox, paste the value of **Logout URL**, which you have copied from Azure portal
		
	c. Open your base-64 encoded certificate in notepad, copy the content of it into your clipboard, and then paste it to the **ID Provider Certificate** textbox
	 
	d. To open the **Settings** page, click **Save**.

4. On the **Settings** page, click **Generate SSO Token**.

	![Settings](./media/druva-tutorial/ic795093.png "Settings")

5. On the **Single Sign-on Authentication Token** dialog, perform the following steps:

	![SSO Token](./media/druva-tutorial/ic795094.png "SSO Token")
	
	a. Click **Copy**, Paste copied value in the **Value** textbox in the **Add Attribute** section in the Azure portal.
	
	b. Click **Close**.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Druva.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Druva**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Druva**.

	![The Druva link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Druva test user

In order to enable Azure AD users to log in to Druva, they must be provisioned into Druva. In the case of Druva, provisioning is a manual task.

**To configure user provisioning, perform the following steps:**

1. Log in to your **Druva** company site as administrator.

2. Go to **Manage \> Users**.
   
	![Manage Users](./media/druva-tutorial/ic795097.png "Manage Users")

3. Click **Create New**.
   
	![Manage Users](./media/druva-tutorial/ic795098.png "Manage Users")

4. On the Create New User dialog, perform the following steps:
   
	![Create NewUser](./media/druva-tutorial/ic795099.png "Create NewUser")
   
    a. In the **Email address** textbox, enter the email of user like **brittasimon\@contoso.com**.
   
    b. In the **Name** textbox, enter the name of user like **BrittaSimon**.
   
    c. Click **Create User**.

>[!NOTE]
>You can use any other Druva user account creation tools or APIs provided by Druva to provision Azure AD user accounts.

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Druva tile in the Access Panel, you should be automatically signed in to the Druva for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

