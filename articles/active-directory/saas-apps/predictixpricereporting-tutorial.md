---
title: 'Tutorial: Azure Active Directory integration with Predictix Price Reporting | Microsoft Docs'
description: In this tutorial, you'll learn how to configure single sign-on between Azure Active Directory and Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes

---
# Tutorial: Azure Active Directory integration with Predictix Price Reporting

In this tutorial, you'll learn how to integrate Predictix Price Reporting with Azure Active Directory (Azure AD).

This integration provides these benefits:

* You can use Azure AD to control who has access to Predictix Price Reporting.
* You can enable your users to be automatically signed in to Predictix Price Reporting (single sign-on) with their Azure AD accounts.
* You can manage your accounts in one central location: the Azure portal.

To learn more about SaaS app integration with Azure AD, see [Single sign-on to applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you start.

## Prerequisites

To configure Azure AD integration with Predictix Price Reporting, you need:

* An Azure AD subscription. If you don't have an Azure AD environment, you can sign up for a [one-month trial](https://azure.microsoft.com/pricing/free-trial/) subscription.
* A Predictix Price Reporting subscription that has single sign-on enabled.

## Scenario description

In this tutorial, you'll configure and test Azure AD single sign-on in a test environment.

* Predictix Price Reporting supports SP-initiated SSO.

## Adding Predictix Price Reporting from the gallery

To set up the integration of Predictix Price Reporting into Azure AD, you need to add Predictix Price Reporting from the gallery to your list of managed SaaS apps.

1. In the [Azure portal](https://portal.azure.com), in the left pane, select **Azure Active Directory**:

	![Select Azure Active Directory](common/select-azuread.png)

2. Go to **Enterprise applications** > **All applications**:

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add an application, select **New application** at the top of the window:

	![Select New application](common/add-new-app.png)

4. In the search box, enter **Predictix Price Reporting**. Select **Predictix Price Reporting** in the search results and then select **Add**.

	 ![Search results](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you'll configure and test Azure AD single sign-on with Predictix Price Reporting by using a test user named Britta Simon.
To enable single sign-on, you need to establish a relationship between an Azure AD user and the corresponding user in Predictix Price Reporting.

To configure and test Azure AD single sign-on with Predictix Price Reporting, you need to complete these steps:

1. **[Configure Azure AD single sign-on](#configure-azure-ad-single-sign-on)** to enable the feature for your users.
2. **[Configure Predictix Price Reporting single sign-on](#configure-predictix-price-reporting-single-sign-on)** on the application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** to test Azure AD single sign-on.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** to enable Azure AD single sign-on for the user.
5. **[Create a Predictix Price Reporting test user](#create-a-predictix-price-reporting-test-user)** that's linked to the Azure AD representation of the user.
6. **[Test single sign-on](#test-single-sign-on)** to verify that the configuration works.

### Configure Azure AD single sign-on

In this section, you'll enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Predictix Price Reporting, take these steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Predictix Price Reporting** application integration page, select **Single sign-on**:

    ![Select Single sign-on](common/select-sso.png)

2. In the **Select a single sign-on method** dialog box, select **SAML/WS-Fed** mode to enable single sign-on:

    ![Select a single sign-on method](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, select the **Edit** icon to open the **Basic SAML Configuration** dialog box:

	![Edit icon](common/edit-urls.png)

4. In the **Basic SAML Configuration** dialog box, complete the following steps.

    ![Basic SAML Configuration dialog box](common/sp-identifier.png)

	1. In the **Sign on URL** box, enter a URL in this pattern:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. In the **Identifier (Entity ID)** box, enter a URL in this pattern:

        | |
	    |--|
	    | `https://<companyname-pricing>.predictix.com` |
	    | `https://<companyname-pricing>.dev.predictix.com` |
	    | |

	> [!NOTE]
	> These values are placeholders. You need to use the actual sign-on URL and identifier. Contact the [Predictix Price Reporting support team](https://www.infor.com/company/customer-center/) to get the values. You can also refer to the patterns shown in the **Basic SAML Configuration** dialog box in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, select the **Download** link next to **Certificate (Base64)**, per your requirements, and save the certificate on your computer:

	![Certificate download link](common/certificatebase64.png)

6. In the **Set up Predictix Price Reporting** section, copy the appropriate URLs, based on your requirements.

	![Copy the configuration URLs](common/copy-configuration-urls.png)

	1. **Login URL**.

	1. **Azure AD Identifier**.

	1. **Logout URL**.

### Configure Predictix Price Reporting single sign-on

To configure single sign-on on the Predictix Price Reporting side, you need to send the certificate that you downloaded and the URLs that you copied from the Azure portal to the [Predictix Price Reporting support team](https://www.infor.com/company/customer-center/). This team ensures the SAML SSO connection is set properly on both sides.

### Create an Azure AD test user

In this section, you'll create a test user named Britta Simon in the Azure portal.

1. In the Azure portal, select **Azure Active Directory** in the left pane, select **Users**, and then select **All users**:

    ![Select All users](common/users.png)

2. Select **New user** at the top of the screen:

    ![Select New user](common/new-user.png)

3. In the **User** dialog box, take the following steps.

    ![User dialog box](common/user-properties.png)

    1. In the **Name** box, enter **BrittaSimon**.
  
    1. In the **User name** box, enter **BrittaSimon@\<yourcompanydomain>.\<extension>**. (For example, BrittaSimon@contoso.com.)

    1. Select **Show Password**, and then write down the value that's in the **Password** box.

    1. Select **Create**.

### Assign the Azure AD test user

In this section, you'll enable Britta Simon to use Azure AD single sign-on by granting her access to Predictix Price Reporting.

1. In the Azure portal, select **Enterprise applications**, select **All applications**, and then select **Predictix Price Reporting**.

	![Enterprise applications](common/enterprise-applications.png)

2. In the list of applications, select **Predictix Price Reporting**.

	![List of applications](common/all-applications.png)

3. In the left pane, select **Users and groups**:

    ![Select Users and groups](common/users-groups-blade.png)

4. Select **Add user**, and then select **Users and groups** in the **Add Assignment** dialog box.

    ![Select Add user](common/add-assign-user.png)

5. In the **Users and groups** dialog box, select **Britta Simon** in the users list, and then click the **Select** button at the bottom of the screen.

6. If you expect a role value in the SAML assertion, in the **Select Role** dialog box, select the appropriate role for the user from the list. Click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog box, select **Assign**.

### Create a Predictix Price Reporting test user

Next, you need to create a user named Britta Simon in Predictix Price Reporting. Work with the [Predictix Price Reporting support team](https://www.infor.com/company/customer-center/) to add users. Users need to be created and activated before you use single sign-on.

### Test single sign-on

Now you need to test your Azure AD single sign-on configuration by using the Access Panel.

When you select the Predictix Price Reporting tile in the Access Panel, you should be automatically signed in to the Predictix Price Reporting instance for which you set up SSO. For more information, see [Access and use apps on the My Apps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)