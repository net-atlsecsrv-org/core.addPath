---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Count Me In - Operations Dashboard | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Count Me In - Operations Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Count Me In - Operations Dashboard

In this tutorial, you'll learn how to integrate Count Me In - Operations Dashboard with Azure Active Directory (Azure AD). When you integrate Count Me In - Operations Dashboard with Azure AD, you can:

* Control in Azure AD who has access to Count Me In - Operations Dashboard.
* Enable your users to be automatically signed-in to Count Me In - Operations Dashboard with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Count Me In - Operations Dashboard single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Count Me In - Operations Dashboard supports **SP** initiated SSO

## Adding Count Me In - Operations Dashboard from the gallery

To configure the integration of Count Me In - Operations Dashboard into Azure AD, you need to add Count Me In - Operations Dashboard from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Count Me In - Operations Dashboard** in the search box.
1. Select **Count Me In - Operations Dashboard** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Count Me In - Operations Dashboard

Configure and test Azure AD SSO with Count Me In - Operations Dashboard using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Count Me In - Operations Dashboard.

To configure and test Azure AD SSO with Count Me In - Operations Dashboard, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Count Me In-Operations Dashboard SSO](#configure-count-me-in-operations-dashboard-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Count Me In-Operations Dashboard test user](#create-count-me-in-operations-dashboard-test-user)** - to have a counterpart of B.Simon in Count Me In - Operations Dashboard that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Count Me In - Operations Dashboard** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `api-us.localz.io/<PROJECT_ID>`

	c. In the **Reply URL** text box, type a URL using the following pattern:
    `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Count Me In - Operations Dashboard Client support team](mailto:support@localz.co) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. Count Me In - Operations Dashboard application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![image](common/default-attributes.png)

1. In addition to above, Count Me In - Operations Dashboard application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.
	
	| Name |  Source Attribute|
	| ----------- | --------- |
	| assigned roles | user.assignedroles |

    > [!NOTE]
    > Count Me In - Operations Dashboard expects roles for users assigned to the application. Please set up these roles in Azure AD so that users can be assigned the appropriate roles. To understand how to configure roles in Azure AD, see [here](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Count Me In - Operations Dashboard** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Count Me In - Operations Dashboard.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Count Me In - Operations Dashboard**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Count Me In-Operations Dashboard SSO

To configure single sign-on on **Count Me In - Operations Dashboard** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Count Me In - Operations Dashboard support team](mailto:support@localz.co). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Count Me In-Operations Dashboard test user

In this section, you create a user called Britta Simon in Count Me In - Operations Dashboard. Work with [Count Me In - Operations Dashboard support team](mailto:support@localz.co) to add the users in the Count Me In - Operations Dashboard platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Count Me In - Operations Dashboard Sign-on URL where you can initiate the login flow. 

* Go to Count Me In - Operations Dashboard Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Count Me In - Operations Dashboard tile in the My Apps, this will redirect to Count Me In - Operations Dashboard Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## Next steps

Once you configure Count Me In - Operations Dashboard you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


