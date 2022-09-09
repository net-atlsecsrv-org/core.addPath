---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Skedda | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Skedda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Skedda

In this tutorial, you'll learn how to integrate Skedda with Azure Active Directory (Azure AD). When you integrate Skedda with Azure AD, you can:

* Control in Azure AD who has access to Skedda.
* Enable your users to be automatically signed-in to Skedda with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Skedda single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Skedda supports **SP and IDP** initiated SSO

* Once you configure Skedda you can enforce session control, which protect exfiltration and infiltration of your organization’s sensitive data in real-time. Session control extend from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## Adding Skedda from the gallery

To configure the integration of Skedda into Azure AD, you need to add Skedda from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Skedda** in the search box.
1. Select **Skedda** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Skedda

Configure and test Azure AD SSO with Skedda using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Skedda.

To configure and test Azure AD SSO with Skedda, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Skedda SSO](#configure-skedda-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Skedda test user](#create-skedda-test-user)** - to have a counterpart of B.Simon in Skedda that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Skedda** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, the user does not have to perform any step as the app is already pre-integrated with Azure.

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://www.skedda.com/account/externallogin?returnUrl=<CUSTOM_URL>`

    > [!NOTE]
	> The value is not real. Update the value with the actual Sign-on URL. Contact [Skedda Client support team](mailto:info@skedda.com) to get the value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. Click **Save**.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Skedda** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Skedda.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Skedda**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Skedda SSO

To configure single sign-on on **Skedda** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Skedda support team](mailto:info@skedda.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Skedda test user

In this section, you create a user called B.Simon in Skedda. Work with [Skedda support team](mailto:info@skedda.com) to add the users in the Skedda platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Skedda tile in the Access Panel, you should be automatically signed in to the Skedda for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [What is conditional access in Azure Active Directory?](../conditional-access/overview.md)

- [Try Skedda with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [How to protect Skedda with advanced visibility and controls](/cloud-app-security/proxy-intro-aad)