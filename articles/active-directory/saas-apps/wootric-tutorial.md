---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Wootric | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Wootric.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Wootric

In this tutorial, you'll learn how to integrate Wootric with Azure Active Directory (Azure AD). When you integrate Wootric with Azure AD, you can:

* Control in Azure AD who has access to Wootric.
* Enable your users to be automatically signed-in to Wootric with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Wootric single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Wootric supports **IDP** initiated SSO
* Wootric supports **Just In Time** user provisioning
* Once you configure Wootric you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## Adding Wootric from the gallery

To configure the integration of Wootric into Azure AD, you need to add Wootric from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Wootric** in the search box.
1. Select **Wootric** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Wootric

Configure and test Azure AD SSO with Wootric using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Wootric.

To configure and test Azure AD SSO with Wootric, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Wootric SSO](#configure-wootric-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Wootric test user](#create-wootric-test-user)** - to have a counterpart of B.Simon in Wootric that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Wootric** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, the application is pre-configured and the necessary URLs are already pre-populated with Azure. The user needs to save the configuration by clicking the **Save** button.


1. Wootric application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![image](common/default-attributes.png)

1. In addition to above, Wootric application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.
	
	| Name |  Source Attribute |
	| -------------- | --------- |
	| id | user.objectid |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Wootric** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Wootric.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Wootric**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Wootric SSO

1. Log in to the Wootric in a different browser window as an administrator.

1. Click on **Settings Icon** from the top menu.

	![Screenshot shows the Settings Icon selected from the Wootric site.](./media/wootric-tutorial/configure-1.PNG)

1. In the **INTEGRATIONS**, select **Authentication** from the Left side menu and click on **Enable Single Sign On with Azure Active Directory**.

	![Screenshot shows Enable Single Sign On with Azure Active Directory connected in the Authentication item.](./media/wootric-tutorial/configure-2.PNG)

1. Perform the following steps in the following page:

	![Screenshot shows the Settings page where you can enter the values described.](./media/wootric-tutorial/configure-3.PNG)

	a. In the **Identity Provider Single Sign-On URL** textbox, paste the **Login URL** value which you have copied from the Azure portal.

	b. In the **Identity Provider Issuer** textbox, paste the **Entity ID** value which you have copied from the Azure portal.

	c. Open the downloaded **Certificate (Base64)** from the Azure portal into Notepad and paste the content into the **X.509 Certificate** textbox.

	d. Select **Automatically grant access to new users** checkbox.
	
	e. Click on **Save**.

### Create Wootric test user

In this section, a user called B.Simon is created in Wootric. Wootric supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Wootric, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Wootric tile in the Access Panel, you should be automatically signed in to the Wootric for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [What is conditional access in Azure Active Directory?](../conditional-access/overview.md)

- [Try Wootric with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [How to protect Wootric with advanced visibility and controls](/cloud-app-security/proxy-intro-aad)