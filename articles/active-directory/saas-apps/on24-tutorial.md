---
title: 'Tutorial: Azure Active Directory integration with ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and ON24 Virtual Environment SAML Connection.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with ON24 Virtual Environment SAML Connection

In this tutorial, you learn how to integrate ON24 Virtual Environment SAML Connection with Azure Active Directory (Azure AD).
Integrating ON24 Virtual Environment SAML Connection with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to ON24 Virtual Environment SAML Connection.
* You can enable your users to be automatically signed-in to ON24 Virtual Environment SAML Connection (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with ON24 Virtual Environment SAML Connection, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* ON24 Virtual Environment SAML Connection single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* ON24 Virtual Environment SAML Connection supports **SP** and **IDP** initiated SSO

## Adding ON24 Virtual Environment SAML Connection from the gallery

To configure the integration of ON24 Virtual Environment SAML Connection into Azure AD, you need to add ON24 Virtual Environment SAML Connection from the gallery to your list of managed SaaS apps.

**To add ON24 Virtual Environment SAML Connection from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **ON24 Virtual Environment SAML Connection**, select **ON24 Virtual Environment SAML Connection** from result panel then click **Add** button to add the application.

	 ![ON24 Virtual Environment SAML Connection in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with ON24 Virtual Environment SAML Connection based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in ON24 Virtual Environment SAML Connection needs to be established.

To configure and test Azure AD single sign-on with ON24 Virtual Environment SAML Connection, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure ON24 Virtual Environment SAML Connection Single Sign-On](#configure-on24-virtual-environment-saml-connection-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create ON24 Virtual Environment SAML Connection test user](#create-on24-virtual-environment-saml-connection-test-user)** - to have a counterpart of Britta Simon in ON24 Virtual Environment SAML Connection that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with ON24 Virtual Environment SAML Connection, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **ON24 Virtual Environment SAML Connection** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, If you wish to configure the application in **IDP** initiated mode, perform the following steps:

    ![ON24 Virtual Environment SAML Connection Domain and URLs single sign-on information](common/idp-relay.png)

    a. In the **Identifier** text box, type a URL:

     **Production Environment URL**
    
	`SAML-VSHOW.on24.com`

	`SAML-Gateway.on24.com` 

	`SAP PROD SAML-EliteAudience.on24.com` 
                
	 **QA Environment URL**
    
	`SAMLQA-VSHOW.on24.com` 

	`SAMLQA-Gateway.on24.com` 

	`SAMLQA-EliteAudience.on24.com`

    b. In the **Reply URL** text box, type a URL:

     **Production Environment URL**
    
	`https://federation.on24.com/sp/ACS.saml2`

	`https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

	`https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

	`https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

	 **QA Environment URL**
    
	`https://qafederation.on24.com/sp/ACS.saml2`

	`https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

	`https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
	`https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

	c. Click **Set additional URLs**. 

	d. In the **Relay State** text box, type a URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  If you wish to configure the application in **SP** initiated mode, perform the following step:

    ![Screenshot that shows the "Set additional U R Ls" section with the "Sign on U R L" text box highlighted.](common/both-signonurl.png)

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://vshow.on24.com/vshow/<INSTANCENAME>`

	> [!NOTE]
	> These values are not real. Update these values with the actual Relay State and Sign-on URL. Contact [ON24 Virtual Environment SAML Connection Client support team](https://www.on24.com/contact-us/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

6. On the **Set up ON24 Virtual Environment SAML Connection** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure AD Identifier

	c. Logout URL

### Configure ON24 Virtual Environment SAML Connection Single Sign-On

To configure single sign-on on **ON24 Virtual Environment SAML Connection** side, you need to send the downloaded **Federation Metadata XML** and appropriate copied URLs from Azure portal to [ON24 Virtual Environment SAML Connection support team](https://www.on24.com/about-us/support/). They set this setting to have the SAML SSO connection set properly on both sides.

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

In this section, you enable Britta Simon to use Azure single sign-on by granting access to ON24 Virtual Environment SAML Connection.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **ON24 Virtual Environment SAML Connection**.

	![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **ON24 Virtual Environment SAML Connection**.

	![The ON24 Virtual Environment SAML Connection link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create ON24 Virtual Environment SAML Connection test user

In this section, you create a user called Britta Simon in ON24 Virtual Environment SAML Connection. Work with [ON24 Virtual Environment SAML Connection support team](https://www.on24.com/about-us/support/) to add the users in the ON24 Virtual Environment SAML Connection platform. Users must be created and activated before you use single sign-on.

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the ON24 Virtual Environment SAML Connection tile in the Access Panel, you should be automatically signed in to the ON24 Virtual Environment SAML Connection for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is Conditional Access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

