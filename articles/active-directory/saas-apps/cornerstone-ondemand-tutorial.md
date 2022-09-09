---
title: 'Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone Single Sign-On | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Cornerstone Single Sign-On.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2021
ms.author: jeedes
---

# Tutorial: Azure Active Directory Single sign-on (SSO) integration with Cornerstone Single Sign-On

In this tutorial, you'll learn how to integrate Cornerstone Single Sign-On with Azure Active Directory (Azure AD). When you integrate Cornerstone Single Sign-On with Azure AD, you can:

* Control in Azure AD who has access to Cornerstone Single Sign-On.
* Enable your users to be automatically signed-in to Cornerstone Single Sign-On with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Cornerstone Single Sign-On single sign-on (SSO) enabled subscription.

> [!NOTE]
> This integration is also available to use from Azure AD US Government Cloud environment. You can find this application in the Azure AD US Government Cloud Application Gallery and configure it in the same way as you do from public cloud.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Cornerstone Single Sign-On supports **SP** initiated SSO.
* Cornerstone Single Sign-On supports [Automated user provisioning](cornerstone-ondemand-provisioning-tutorial.md).
* If you are integrating one or multiple products from this particular list then you should use this Cornerstone OnDemand Single Sign-On app from the Gallery.

    We offer solutions for :

    1. Learning Management (LMS)
    2. Performance Management (EPM)
    3. Succession Planning
    4. Recruiting (ATS)
    5. Extended Enterprise
    6. Human Resources
    7. Employee Content

## Adding Cornerstone Single Sign-On from the gallery

To configure the integration of Cornerstone Single Sign-On into Azure AD, you need to add Cornerstone Single Sign-On from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Cornerstone Single Sign-On** in the search box.
1. Select **Cornerstone Single Sign-On** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Cornerstone Single Sign-On

Configure and test Azure AD SSO with Cornerstone Single Sign-On using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Cornerstone Single Sign-On.

To configure and test Azure AD SSO with Cornerstone Single Sign-On, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
2. **[Configure Cornerstone Single Sign-On SSO](#configure-cornerstone-single-sign-on-sso)** - to configure the Single Sign-On settings on application side.
    1. **[Create Cornerstone Single Sign-On test user](#create-cornerstone-single-sign-on-test-user)** - to have a counterpart of B.Simon in Cornerstone Single Sign-On that is linked to the Azure AD representation of user.
3. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Cornerstone Single Sign-On** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<PORTAL_NAME>.csod.com`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    > [!NOTE]
    > These values are not real. Update these values with the actual Reply URL, Identifier and Sign on URL. Contact [Cornerstone Single Sign-On Client support team](mailto:moreinfo@csod.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/certificatebase64.png)

6. On the **Set up Cornerstone Single Sign-On** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Cornerstone Single Sign-On.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Cornerstone Single Sign-On**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Cornerstone Single Sign-On SSO

To configure single sign-on on **Cornerstone Single Sign-On** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Cornerstone Single Sign-On support team](mailto:moreinfo@csod.com) or please contact your partner. They set this setting to have the SAML SSO connection set properly on both sides.

### Create Cornerstone Single Sign-On test user

The objective of this section is to create a user called B.Simon in Cornerstone Single Sign-On. Cornerstone Single Sign-On supports automatic user provisioning, which is by default enabled. You can find more details [here](./cornerstone-ondemand-provisioning-tutorial.md) on how to configure automatic user provisioning.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Cornerstone Single Sign-On Sign-on URL where you can initiate the login flow. 

* Go to Cornerstone Single Sign-On Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Cornerstone Single Sign-On tile in the My Apps, this will redirect to Cornerstone Single Sign-On Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Cornerstone Single Sign-On you can enforce Session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
