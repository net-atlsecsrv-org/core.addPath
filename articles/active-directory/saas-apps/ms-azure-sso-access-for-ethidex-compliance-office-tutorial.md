---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with MS Azure SSO Access for Ethidex Compliance Office™ | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and MS Azure SSO Access for Ethidex Compliance Office™.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with MS Azure SSO Access for Ethidex Compliance Office™

In this tutorial, you'll learn how to integrate MS Azure SSO Access for Ethidex Compliance Office™ with Azure Active Directory (Azure AD). When you integrate MS Azure SSO Access for Ethidex Compliance Office™ with Azure AD, you can:

* Control in Azure AD who has access to MS Azure SSO Access for Ethidex Compliance Office™.
* Enable your users to be automatically signed-in to MS Azure SSO Access for Ethidex Compliance Office™ with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* MS Azure SSO Access for Ethidex Compliance Office™ single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* MS Azure SSO Access for Ethidex Compliance Office™ supports **IDP** initiated SSO

## Adding MS Azure SSO Access for Ethidex Compliance Office™ from the gallery

To configure the integration of MS Azure SSO Access for Ethidex Compliance Office™ into Azure AD, you need to add MS Azure SSO Access for Ethidex Compliance Office™ from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **MS Azure SSO Access for Ethidex Compliance Office™** in the search box.
1. Select **MS Azure SSO Access for Ethidex Compliance Office™** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for MS Azure SSO Access for Ethidex Compliance Office™

Configure and test Azure AD SSO with MS Azure SSO Access for Ethidex Compliance Office™ using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in MS Azure SSO Access for Ethidex Compliance Office™.

To configure and test Azure AD SSO with MS Azure SSO Access for Ethidex Compliance Office™, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure MS Azure SSO Access for Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** - to configure the single sign-on settings on application side.
    1. **[Create MS Azure SSO Access for Ethidex Compliance Office test user](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** - to have a counterpart of B.Simon in MS Azure SSO Access for Ethidex Compliance Office™ that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **MS Azure SSO Access for Ethidex Compliance Office™** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `com.ethidex.prod.<CLIENTID>`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
	> These values are not real. Update these values with the actual Identifier and Reply URL. Contact [MS Azure SSO Access for Ethidex Compliance Office™ support team](mailto:support@ethidex.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. MS Azure SSO Access for Ethidex Compliance Office™ application application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes, where as **nameidentifier** is mapped with **user.userprincipalname**. MS Azure SSO Access for Ethidex Compliance Office™ application expects **nameidentifier** to be mapped with **user.mail**, so you need to edit the attribute mapping by clicking on **Edit** icon and change the attribute mapping.

	![image](common/edit-attribute.png)

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Raw)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificateraw.png)

1. On the **Set up MS Azure SSO Access for Ethidex Compliance Office™** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to MS Azure SSO Access for Ethidex Compliance Office™.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **MS Azure SSO Access for Ethidex Compliance Office™**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure MS Azure SSO Access for Ethidex Compliance Office SSO

To configure single sign-on on **MS Azure SSO Access for Ethidex Compliance Office™** side, you need to send the downloaded **Certificate (Raw)** and appropriate copied URLs from Azure portal to [MS Azure SSO Access for Ethidex Compliance Office™ support team](mailto:support@ethidex.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create MS Azure SSO Access for Ethidex Compliance Office test user

In this section, you create a user called B.Simon in MS Azure SSO Access for Ethidex Compliance Office™. Work with [MS Azure SSO Access for Ethidex Compliance Office™ support team](mailto:support@ethidex.com) to add the users in the MS Azure SSO Access for Ethidex Compliance Office™ platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the MS Azure SSO Access for Ethidex Compliance Office™ tile in the Access Panel, you should be automatically signed in to the MS Azure SSO Access for Ethidex Compliance Office™ for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [What is conditional access in Azure Active Directory?](../conditional-access/overview.md)

- [Try MS Azure SSO Access for Ethidex Compliance Office™ with Azure AD](https://aad.portal.azure.com/)