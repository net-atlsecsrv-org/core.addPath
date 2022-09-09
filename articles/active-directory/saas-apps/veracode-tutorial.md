---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Veracode | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Veracode

In this tutorial, you'll learn how to integrate Veracode with Azure Active Directory (Azure AD). When you integrate Veracode with Azure AD, you can:

* Control in Azure AD who has access to Veracode.
* Enable your users to be automatically signed-in to Veracode with their Azure AD accounts.
* Manage your accounts in one central location: the Azure portal.

To learn more about software as a service (SaaS) app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* A Veracode single sign-on (SSO)-enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment. Veracode supports identity provider initiated SSO and just-in-time user provisioning.

## Add Veracode from the gallery

To configure the integration of Veracode into Azure AD, add Veracode from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) by using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Go to **Enterprise Applications**, and then select **All Applications**.
1. To add a new application, select **New application**.
1. In the **Add from the gallery** section, type "Veracode" in the search box.
1. Select **Veracode** from the results panel, and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for Veracode

Configure and test Azure AD SSO with Veracode by using a test user called **B.Simon**. For SSO to work, you must establish a link between an Azure AD user and the related user in Veracode.

To configure and test Azure AD SSO with Veracode, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Veracode SSO](#configure-veracode-sso)** to configure the single sign-on settings on the application side.
    * **[Create a Veracode test user](#create-veracode-test-user)** to have a counterpart of B.Simon in Veracode linked to the Azure AD representation of the user.
1. **[Test SSO](#test-sso)** to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Veracode** application integration page, find the **Manage** section. Select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, select the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Screenshot of Set up Single Sign-On with SAML, with pencil icon highlighted](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, the application is pre-configured and the necessary URLs are already pre-populated with Azure. Select **Save**.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)**. Select **Download** to download the certificate and save it on your computer.

	![Screenshot of SAML Signing Certificate section, with Download link highlighted](common/certificatebase64.png)

1. Veracode expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![Screenshot of User Attributes & Claims section](common/default-attributes.png)

1. Veracode also expects a few more attributes to be passed back in the SAML response. These attributes are also pre-populated, but you can review them per your requirements.

	| Name | Source attribute|
	| ---------------| --------------- |
	| firstname |User.givenname |
	| lastname |User.surname |
	| email |User.mail |

1. On the **Set up Veracode** section, copy the appropriate URL(s) based on your requirement.

	![Screenshot of Set up Veracode section, with configuration URLs highlighted](common/copy-configuration-urls.png)

## Configure Veracode SSO

1. In a different web browser window, sign in to your Veracode company site as an administrator.

1. From the menu on the top, select **Settings** > **Admin**.
   
    ![Screenshot of Veracode Administration, with Settings icon and Admin highlighted](./media/veracode-tutorial/ic802911.png "Administration")

1. Select the **SAML** tab.

1. In the **Organization SAML Settings** section, perform the following steps:

    ![Screenshot of Organization SAML Settings section](./media/veracode-tutorial/ic802912.png "Administration")

    a.  For **Issuer**, paste the value of the **Azure AD Identifier** that you've copied from the Azure portal.

    b. For **Assertion Signing Certificate**, select **Choose File** to upload your downloaded certificate from the Azure portal.

    c. For **Self Registration**, select **Enable Self Registration**.

1. In the **Self Registration Settings** section, perform the following steps, and then select **Save**:

    ![Screenshot of Self Registration Settings section, with various options highlighted](./media/veracode-tutorial/ic802913.png "Administration")

    a. For **New User Activation**, select **No Activation Required**.

    b. For **User Data Updates**, select **Preference Veracode User Data**.

    c. For **SAML Attribute Details**, select the following:
      * **User Roles**
      * **Policy Administrator**
      * **Reviewer**
      * **Security Lead**
      * **Executive**
      * **Submitter**
      * **Creator**
      * **All Scan Types**
      * **Team Memberships**
      * **Default Team**

### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory** >**Users** > **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:

   1. For **Name**, enter `B.Simon`.  
   1. For **User name**, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select **Show password**, and then write down the value shown.
   1. Select **Create**.

### Assign the Azure AD test user

In this section, enable B.Simon to use Azure single sign-on by granting access to Veracode.

1. In the Azure portal, select **Enterprise Applications** > **All applications**.
1. In the applications list, select **Veracode**.
1. In the app's overview page, find the **Manage** section, and select **Users and groups**.

   ![Screenshot of Manage section, with Users and groups highlighted](common/users-groups-blade.png)

1. Select **Add user**. In the **Add Assignment** dialog box, select **Users and groups**.

	![Screenshot of Users and groups page, with Add user highlighted](common/add-assign-user.png)

1. In the **Users and groups** dialog box, from **Users**, select **B.Simon**. Then choose **Select** at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog box, select the appropriate role for the user from the list. Then choose **Select** at the bottom of the screen.
1. In the **Add Assignment** dialog box, select **Assign**.

### Create Veracode test user

To sign in to Veracode, Azure AD users must be provisioned into Veracode. This task is automated, and you don't need to do anything manually. Users are automatically created if necessary during the first single sign-on attempt.

> [!NOTE]
> You can use any other Veracode user account creation tools or APIs provided by Veracode to provision Azure AD user accounts.

## Test SSO

In this section, you test your Azure AD single sign-on configuration by using the Access Panel.

When you select **Veracode** in the Access Panel, you should be automatically signed in to the Veracode for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of tutorials on how to integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Veracode with Azure AD](https://aad.portal.azure.com/)