---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with RedBrick Health | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and RedBrick Health.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with RedBrick Health

In this tutorial, you'll learn how to integrate RedBrick Health with Azure Active Directory (Azure AD). When you integrate RedBrick Health with Azure AD, you can:

* Control in Azure AD who has access to RedBrick Health.
* Enable your users to be automatically signed-in to RedBrick Health with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* RedBrick Health single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* RedBrick Health supports **IDP** initiated SSO

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Adding RedBrick Health from the gallery

To configure the integration of RedBrick Health into Azure AD, you need to add RedBrick Health from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **RedBrick Health** in the search box.
1. Select **RedBrick Health** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for RedBrick Health

Configure and test Azure AD SSO with RedBrick Health using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in RedBrick Health.

To configure and test Azure AD SSO with RedBrick Health, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure RedBrick Health SSO](#configure-redbrick-health-sso)** - to configure the single sign-on settings on application side.
    1. **[Create RedBrick Health test user](#create-redbrick-health-test-user)** - to have a counterpart of B.Simon in RedBrick Health that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **RedBrick Health** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL:
    `https://www.redbrickhealth.com`

    b. In the **Reply URL** text box, type a URL:
    `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`

	For Production Environment: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Click **Set additional URLs**.

    d. In the **Relay State** text box, type a URL using the following pattern:
    `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`

	> [!NOTE]
	> Relay State value is not real. Update this value with the actual Relay State. Contact [RedBrick Health Client support team](https://home.redbrickhealth.com/contact/) to get this value. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. RedBrick Health application expects the SAML assertions in a specific format. Configure the following claims for this application. You can manage the values of these attributes from the **User Attributes** section on application integration page. On the **Set up Single Sign-On with SAML** page, click **Edit** button to open **User Attributes** dialog.

	![image](common/edit-attribute.png)

1. In the **User Claims** section on the **User Attributes** dialog, configure SAML token attribute as shown in the image above and perform the following steps:

	| Name | Source Attribute|
	| ----------- | --------- |
	| principal name | ********** |
	| client id | ********** |
	| participant id | ********** |

	> [!NOTE]
	> These values are for reference purpose only. You need to define the attributes as per your organization's requirement. Please contact [RedBrick Health support team](https://home.redbrickhealth.com/contact/) to get more info about the required claims.

	a. Click **Add new claim** to open the **Manage user claims** dialog.

	![image](common/new-save-attribute.png)

	![image](common/new-attribute-details.png)

	b. In the **Name** textbox, type the attribute name shown for that row.

	c. Leave the **Namespace** blank.

	d. Select Source as **Attribute**.

	e. From the **Source attribute** list, type the attribute value shown for that row.

	f. Click **Ok**

	g. Click **Save**.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up RedBrick Health** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to RedBrick Health.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **RedBrick Health**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure RedBrick Health SSO

To configure single sign-on on **RedBrick Health** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [RedBrick Health support team](https://home.redbrickhealth.com/contact/). They set this setting to have the SAML SSO connection set properly on both sides.

### Create RedBrick Health test user

In this section, you create a user called B.Simon in RedBrick Health. Work with [RedBrick Health support team](https://home.redbrickhealth.com/contact/) to add the users in the RedBrick Health platform. Users must be created and activated before you use single sign-on.

## Test SSO

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the RedBrick Health tile in the Access Panel, you should be automatically signed in to the RedBrick Health for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try RedBrick Health with Azure AD](https://aad.portal.azure.com/)