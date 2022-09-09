---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with In Case of Crisis - Online Portal | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and In Case of Crisis - Online Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with In Case of Crisis - Online Portal

In this tutorial, you'll learn how to integrate In Case of Crisis - Online Portal with Azure Active Directory (Azure AD). When you integrate In Case of Crisis - Online Portal with Azure AD, you can:

* Control in Azure AD who has access to In Case of Crisis - Online Portal.
* Enable your users to be automatically signed-in to In Case of Crisis - Online Portal with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* In Case of Crisis - Online Portal single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* In Case of Crisis - Online Portal supports **IDP** initiated SSO
* Once you configure the In Case of Crisis - Online Portal you can enforce session controls, which protect exfiltration and infiltration of your organization’s sensitive data in real-time. Session controls extend from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## Adding In Case of Crisis - Online Portal from the gallery

To configure the integration of In Case of Crisis - Online Portal into Azure AD, you need to add In Case of Crisis - Online Portal from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **In Case of Crisis - Online Portal** in the search box.
1. Select **In Case of Crisis - Online Portal** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD single sign-on for In Case of Crisis - Online Portal

Configure and test Azure AD SSO with In Case of Crisis - Online Portal using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in In Case of Crisis - Online Portal.

To configure and test Azure AD SSO with In Case of Crisis - Online Portal, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure In Case of Crisis Online Portal SSO](#configure-in-case-of-crisis-online-portal-sso)** - to configure the single sign-on settings on application side.
    * **[Create In Case of Crisis Online Portal test user](#create-in-case-of-crisis-online-portal-test-user)** - to have a counterpart of B.Simon in In Case of Crisis - Online Portal that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **In Case of Crisis - Online Portal** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, the application is pre-configured and the necessary URLs are already pre-populated with Azure. The user needs to save the configuration by clicking the **Save** button.


1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up In Case of Crisis - Online Portal** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to In Case of Crisis - Online Portal.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **In Case of Crisis - Online Portal**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure In Case of Crisis Online Portal SSO

To configure single sign-on on **In Case of Crisis - Online Portal** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [In Case of Crisis - Online Portal support team](mailto:support@rockdovesolutions.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create In Case of Crisis Online Portal test user

In this section, you create a user called B.Simon in In Case of Crisis - Online Portal. Work with [In Case of Crisis - Online Portal support team](mailto:support@rockdovesolutions.com) to add the users in the In Case of Crisis - Online Portal platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the In Case of Crisis - Online Portal tile in the Access Panel, you should be automatically signed in to the In Case of Crisis - Online Portal for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try In Case of Crisis - Online Portal with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)