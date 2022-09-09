---
title: 'Tutorial: Azure Active Directory integration with HeyBuddy | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and HeyBuddy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---
# Tutorial: Azure Active Directory integration with HeyBuddy

In this tutorial, you learn how to integrate HeyBuddy with Azure Active Directory (Azure AD).
Integrating HeyBuddy with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to HeyBuddy.
* You can enable your users to be automatically signed-in to HeyBuddy (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with HeyBuddy, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* HeyBuddy single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* HeyBuddy supports **SP** initiated SSO
* HeyBuddy supports **Just In Time** user provisioning

## Adding HeyBuddy from the gallery

To configure the integration of HeyBuddy into Azure AD, you need to add HeyBuddy from the gallery to your list of managed SaaS apps.

**To add HeyBuddy from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **HeyBuddy**, select **HeyBuddy** from result panel then click **Add** button to add the application.

	 ![HeyBuddy in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with HeyBuddy based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in HeyBuddy needs to be established.

To configure and test Azure AD single sign-on with HeyBuddy, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure HeyBuddy Single Sign-On](#configure-heybuddy-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create HeyBuddy test user](#create-heybuddy-test-user)** - to have a counterpart of Britta Simon in HeyBuddy that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with HeyBuddy, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **HeyBuddy** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![HeyBuddy Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://api.heybuddy.com/auth/<ENTITY ID>`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `YourCompanyInstanceofHeyBuddy`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign-On URL and Identifier (Entity ID). The `Entity ID` in the Sign on url is auto generated for each organization. Contact [HeyBuddy Client support team](mailto:support@heybuddy.com) to get these values.

5. Your HeyBuddy application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes. Click **Edit** icon to open User Attributes dialog.

	![image](common/edit-attribute.png)

	> [!NOTE]
	> Please refer to this [link](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) on how to configure and setup the roles for the application.

6. In addition to above, HeyBuddy application expects few more attributes to be passed back in SAML response. In the **User Claims** section on the **User Attributes** dialog, perform the following steps to add SAML token attribute as shown in the below table:

	| Name |  Source Attribute|
	| -------- | --------- |
	| Roles  | user.assignedroles |
	| | |

	a. Click **Add new claim** to open the **Manage user claims** dialog.

	![image](common/new-save-attribute.png)

	![image](common/new-attribute-details.png)

	b. In the **Name** textbox, type the attribute name shown for that row.

	c. Leave the **Namespace** blank.

	d. Select Source as **Attribute**.

	e. From the **Source attribute** list, type the attribute value shown for that row.

	f. Click **Ok**

	g. Click **Save**.

7. On the **Set up Single Sign-On with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

	![The Certificate download link](common/copy-metadataurl.png)

### Configure HeyBuddy Single Sign-On

To configure single sign-on on **HeyBuddy** side, you need to send the **App Federation Metadata Url** to [HeyBuddy support team](mailto:support@heybuddy.com). They set this setting to have the SAML SSO connection set properly on both sides.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to HeyBuddy.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **HeyBuddy**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **HeyBuddy**.

	![The HeyBuddy link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create HeyBuddy test user

In this section, a user called Britta Simon is created in HeyBuddy. HeyBuddy supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in HeyBuddy, a new one is created after authentication.

> [!Note]
> If you need to create a user manually, contact [HeyBuddy support team](mailto:support@heybuddy.com).

### Test single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the HeyBuddy tile in the Access Panel, you should be automatically signed in to the HeyBuddy for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)