---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Parkalot - Car park management | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Parkalot - Car park management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes

---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Parkalot - Car park management

In this tutorial, you'll learn how to integrate Parkalot - Car park management with Azure Active Directory (Azure AD). When you integrate Parkalot - Car park management with Azure AD, you can:

* Control in Azure AD who has access to Parkalot - Car park management.
* Enable your users to be automatically signed-in to Parkalot - Car park management with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Parkalot - Car park management single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Parkalot - Car park management supports **SP** initiated SSO

* Parkalot - Car park management supports **Just In Time** user provisioning

## Adding Parkalot - Car park management from the gallery

To configure the integration of Parkalot - Car park management into Azure AD, you need to add Parkalot - Car park management from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Parkalot - Car park management** in the search box.
1. Select **Parkalot - Car park management** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for Parkalot - Car park management

Configure and test Azure AD SSO with Parkalot - Car park management using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Parkalot - Car park management.

To configure and test Azure AD SSO with Parkalot - Car park management, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Parkalot-Car park management SSO](#configure-parkalot-car-park-management-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Parkalot-Car park management test user](#create-parkalot-car-park-management-test-user)** - to have a counterpart of B.Simon in Parkalot - Car park management that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Parkalot - Car park management** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. In the **Identifier (Entity ID)** text box, type a URL using one of the following patterns:

    | Identifier (Entity ID) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. In the **Reply URL** text box, type a URL using one of the following patterns:

    | Reply URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. In the **Sign-on URL** text box, type a URL using one of the following patterns:

    | Sign-on URL |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [Parkalot - Car park management Client support team](mailto:contact-us@parkalot.io) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Parkalot - Car park management** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Parkalot - Car park management.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Parkalot - Car park management**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Parkalot-Car park management SSO

1. In a different web browser window, sign in to your Parkalot - Car park management company site as an administrator.

1. Select **Setup SAML** and click on the **Edit** icon on **Add New** card.

    ![Add New EDIT icon.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Perform the below mentioned steps in the following page.

    ![Configure Parkalot - Car park management SSO.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. In the **Display Name** textbox, give a valid name to it.

    b. In the **IdP Entity ID** textbox, paste the **Azure AD Identifier** value, which you have copied from the Azure portal.

    c. In the **SSO url** textbox, paste the **Login URL** value, which you have copied from the Azure portal.

    d. Open the downloaded **Certificate (Base64)** from the Azure portal into Notepad and paste the content into the **Certificate** textbox.

    e. Click **SAVE**.

### Create Parkalot-Car park management test user

In this section, a user called Britta Simon is created in Parkalot - Car park management. Parkalot - Car park management supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in Parkalot - Car park management, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Parkalot - Car park management Sign-on URL where you can initiate the login flow. 

* Go to Parkalot - Car park management Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Parkalot - Car park management tile in the My Apps, this will redirect to Parkalot - Car park management Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure Parkalot - Car park management you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).