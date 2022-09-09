---
title: 'Tutorial: Azure Active Directory integration with ArcGIS Enterprise | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with ArcGIS Enterprise

In this tutorial, you'll learn how to integrate ArcGIS Enterprise with Azure Active Directory (Azure AD). When you integrate ArcGIS Enterprise with Azure AD, you can:

* Control in Azure AD who has access to ArcGIS Enterprise.
* Enable your users to be automatically signed-in to ArcGIS Enterprise with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* ArcGIS Enterprise single sign-on (SSO) enabled subscription.

> [!NOTE]
> This integration is also available to use from Azure AD US Government Cloud environment. You can find this application in the Azure AD US Government Cloud Application Gallery and configure it in the same way as you do from public cloud.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* ArcGIS Enterprise supports **SP and IDP** initiated SSO.
* ArcGIS Enterprise supports **Just In Time** user provisioning.

## Add ArcGIS Enterprise from the gallery

To configure the integration of ArcGIS Enterprise into Azure AD, you need to add ArcGIS Enterprise from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **ArcGIS Enterprise** in the search box.
1. Select **ArcGIS Enterprise** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for ArcGIS Enterprise

Configure and test Azure AD SSO with ArcGIS Enterprise using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in ArcGIS Enterprise.

To configure and test Azure AD SSO with ArcGIS Enterprise, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure ArcGIS Enterprise SSO](#configure-arcgis-enterprise-sso)** - to configure the single sign-on settings on application side.
    1. **[Create ArcGIS Enterprise test user](#create-arcgis-enterprise-test-user)** - to have a counterpart of B.Simon in ArcGIS Enterprise that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **ArcGIS Enterprise** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps, if you wish to configure the application in **IDP** Initiated mode:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `<EXTERNAL_DNS_NAME>.portal`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [ArcGIS Enterprise Client support team](mailto:support@esri.com) to get these values. You will get the Identifier value from **Set Identity Provider section**, which is explained later in this tutorial.

5. On the **Set up Single Sign-On with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

    ![The Certificate download link](common/copy-metadataurl.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to ArcGIS Enterprise.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **ArcGIS Enterprise**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure ArcGIS Enterprise SSO

1. To automate the configuration within ArcGIS Enterprise, you need to install **My Apps Secure Sign-in browser extension** by clicking **Install the extension**.

    ![My apps extension](common/install-myappssecure-extension.png)

1. After adding extension to the browser, click on **Set up ArcGIS Enterprise** will direct you to the ArcGIS Enterprise application. From there, provide the admin credentials to sign into ArcGIS Enterprise. The browser extension will automatically configure the application for you and automate steps 3-7.

    ![Setup configuration](common/setup-sso.png)

1. If you want to setup ArcGIS Enterprise manually, log in to your ArcGIS Enterprise company site as an administrator.


1. Select **Organization >EDIT SETTINGS**.

    ![Screenshot shows the ArcGIS Enterprise Organization tab with Edit settings called out.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Select **Security** tab.

    ![Screenshot shows the Security tab selected.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Scroll down to the **Enterprise Logins via SAML** section and select **SET ENTERPRISE LOGIN**.

    ![Screenshot shows Enterprise Logins via SAML where you can select Set Enterprise Login.](./media/arcgisenterprise-tutorial/configure-3.png)

1. On the **Set Identity Provider** section, perform the following steps:

    ![Screenshot shows Set Identity Provider where you perform the steps described here.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Please provide a name like **Azure Active Directory Test** in the **Name** textbox.

    b. In the **URL** textbox, paste the **App Federation Metadata Url** value which you have copied from the Azure portal.

    c. Click **Show advanced settings** and copy the **Entity ID** value and paste it into the **Identifier** textbox in the **ArcGIS Enterprise Domain and URLs** section in Azure portal.

    ![Screenshot shows where to get the Entity I D and update identify provider.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Click **UPDATE IDENTITY PROVIDER**.

### Create ArcGIS Enterprise test user

In this section, a user called Britta Simon is created in ArcGIS Enterprise. ArcGIS Enterprise supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in ArcGIS Enterprise, a new one is created after authentication.

> [!Note]
> If you need to create a user manually, contact [ArcGIS Enterprise support team](mailto:support@esri.com).

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to ArcGIS Enterprise Sign on URL where you can initiate the login flow.  

* Go to ArcGIS Enterprise Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the ArcGIS Enterprise for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the ArcGIS Enterprise tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the ArcGIS Enterprise for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure ArcGIS Enterprise you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).