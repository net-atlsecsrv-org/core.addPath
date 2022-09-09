---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Sonarqube | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Sonarqube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Sonarqube

In this tutorial, you'll learn how to integrate Sonarqube with Azure Active Directory (Azure AD). When you integrate Sonarqube with Azure AD, you can:

* Control in Azure AD who has access to Sonarqube.
* Enable your users to be automatically signed-in to Sonarqube with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Sonarqube single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Sonarqube supports **SP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Adding Sonarqube from the gallery

To configure the integration of Sonarqube into Azure AD, you need to add Sonarqube from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Sonarqube** in the search box.
1. Select **Sonarqube** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for Sonarqube

Configure and test Azure AD SSO with Sonarqube using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Sonarqube.

To configure and test Azure AD SSO with Sonarqube, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Sonarqube SSO](#configure-sonarqube-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Sonarqube test user](#create-sonarqube-test-user)** - to have a counterpart of B.Simon in Sonarqube that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Sonarqube** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    In the **Sign-on URL** text box, type a URL:

	* **For Production Environment**

    	`https://servicessonar.corp.microsoft.com/`

	* **For Dev Environment**

		`https://servicescode-dev.westus.cloudapp.azure.com`

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Sonarqube** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Sonarqube.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Sonarqube**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Sonarqube SSO

1. Open a new web browser window and sign into your Sonarqube company site as an administrator.

2. Install the SAML plugin from the sonarqube market place.

3. On the top left of the page, click on **ADMIN** and then navigate to **SAML**.

4. On the **SAML** page, perform the following steps:

	![Sonarqube configuration](./media/sonarqube-tutorial/config01.png)

	a. Toggle the **Enabled** option to **yes**.

	b. In **Application ID** text box, enter the name like **sonarqube**.

	c. In **Provider Name** text box, enter the name like **SAML**.

	d. In **Provider ID** text box, paste the value of **Azure AD Identifier**, which you have copied from Azure portal.

	e. In **SAML login url** text box, paste the value of **Login URL**, which you have copied from Azure portal.

	f. Open the Base64 encoded certificate in notepad, copy its content and paste it into the **Provider certificate** text box.

	g. In **SAML user login attribute** text box, enter the value `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

	h. In **SAML user name attribute** text box, enter the value `http://schemas.microsoft.com/identity/claims/displayname`.

	i. In **SAML user email attribute** text box, enter the value `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

	j. Click **Save**.

### Create Sonarqube test user

In this section, you create a user called B.Simon in Sonarqube. Work with [Sonarqube Client support team](https://www.sonarsource.com/support/) to add the users in the Sonarqube platform. Users must be created and activated before you use single sign-on. 

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Sonarqube tile in the Access Panel, you should be automatically signed in to the Sonarqube for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [What is conditional access in Azure Active Directory?](../conditional-access/overview.md)

- [Try Sonarqube with Azure AD](https://aad.portal.azure.com/)