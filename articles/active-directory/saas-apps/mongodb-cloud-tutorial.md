---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with MongoDB Cloud | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and MongoDB Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes

ms.collection: M365-identity-device-management
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with MongoDB Cloud

In this tutorial, you'll learn how to integrate MongoDB Cloud with Azure Active Directory (Azure AD). When you integrate MongoDB Cloud with Azure AD, you can:

* Control in Azure AD who has access to MongoDB Cloud, MongoDB Atlas, the MongoDB community, MongoDB University, and MongoDB Support.
* Enable your users to be automatically signed in to MongoDB Cloud with their Azure AD accounts.
* Manage your accounts in one central location: the Azure portal.

To learn more about software as a service (SaaS) app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## Prerequisites

To get started, you need:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* A MongoDB Cloud organization that is enabled for single sign-on (SSO), you can signup for a [free cluster](https://www.mongodb.com/cloud)

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* MongoDB Cloud supports **SP** and **IDP** initiated SSO.
* MongoDB Cloud supports **Just In Time** user provisioning.
* After you configure MongoDB Cloud, you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from conditional access. For more information, see [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## Add MongoDB Cloud from the gallery

To configure the integration of MongoDB Cloud into Azure AD, you need to add MongoDB Cloud from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) by using either a work or school account, or a personal Microsoft account.
1. On the left pane, select **Azure Active Directory**.
1. Go to **Enterprise Applications**, and then select **All Applications**.
1. To add a new application, select **New application**.
1. In the **Add from the gallery** section, type **MongoDB Cloud** in the search box.
1. Select **MongoDB Cloud** from the results, and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD single sign-on for MongoDB Cloud

Configure and test Azure AD SSO with MongoDB Cloud, by using a test user called **B.Simon**. For SSO to work, you need to establish a linked relationship between an Azure AD user and the related user in MongoDB Cloud.

To configure and test Azure AD SSO with MongoDB Cloud, complete the following building blocks:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) to enable your users to use this feature.
    1. [Create an Azure AD test user](#create-an-azure-ad-test-user) to test Azure AD single sign-on with B.Simon.
    1. [Assign the Azure AD test user](#assign-the-azure-ad-test-user) to enable B.Simon to use Azure AD single sign-on.
1. [Configure MongoDB Cloud SSO](#configure-mongodb-cloud-sso) to configure the single sign-on settings on the application side.
    1. [Create a MongoDB Cloud test user](#create-a-mongodb-cloud-test-user) to have a counterpart of B.Simon in MongoDB Cloud, linked to the Azure AD representation of the user.
1. [Test SSO](#test-sso) to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **MongoDB Cloud** application integration page, find the **Manage** section. Select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, select the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Screenshot of Set up Single Sign-On with SAML page, with pencil icon highlighted](common/edit-urls.png)

1. In the **Basic SAML Configuration** section, if you want to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL that uses the following pattern:
    `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. In the **Reply URL** text box, type a URL that uses the following pattern:
    `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Select **Set additional URLs**, and perform the following step if you want to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL that uses the following pattern:
    `https://cloud.mongodb.com/sso/<Customer_Unique>`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL, and Sign-on URL. To get these values, contact the [MongoDB Cloud Client support team](https://support.mongodb.com/). You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. The MongoDB Cloud application expects the SAML assertions to be in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![Screenshot of default attributes](common/default-attributes.png)

1. In addition to the preceding attributes, the MongoDB Cloud application expects a few more attributes to be passed back in the SAML response. These attributes are also pre-populated, but you can review them per your requirements.
	
	| Name | Source attribute|
	| ---------------| --------- |
	| email | user.userprincipalname |
	| firstName | user.givenname |
	| lastName | user.surname |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section, find **Federation Metadata XML**. Select **Download** to download the certificate and save it on your computer.

	![Screenshot of SAML Signing Certificate section, with Download link highlighted](common/metadataxml.png)

1. In the **Set up MongoDB Cloud** section, copy the appropriate URLs, based on your requirement.

	![Screenshot of Set up Mongo DB Cloud section, with URLs highlighted](common/copy-configuration-urls.png)
### Create an Azure AD test user

In this section, you create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory** > **Users** > **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write the password down.
   1. Select **Create**.

### Assign the Azure AD test user

In this section, you enable B.Simon to use Azure single sign-on by granting access to MongoDB Cloud.

1. In the Azure portal, select **Enterprise Applications** > **All applications**.
1. In the applications list, select **MongoDB Cloud**.
1. In the app's overview page, find the **Manage** section, and select **Users and groups**.

   ![Screenshot of the Manage section, with Users and groups highlighted](common/users-groups-blade.png)

1. Select **Add user**. Then, in the **Add Assignment** dialog box, select **Users and groups**.

	![Screenshot of Users and groups page, with Add user highlighted](common/add-assign-user.png)

1. In the **Users and groups** dialog box, select **B.Simon** from the list of users. Then choose **Select** at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog box, select the appropriate role for the user from the list. Then choose **Select** at the bottom of the screen.
1. In the **Add Assignment** dialog box, select **Assign**.

## Configure MongoDB Cloud SSO

To configure single sign-on on the MongoDB Cloud side, you need the appropriate URLs copied from the Azure portal. You also need to configure the Federation Application for your MongoDB Cloud Organization. Follow the instructions in the [MongoDB Cloud documentation](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). If you have a problem, contact the [MongoDB Cloud support team](https://support.mongodb.com/).

### Create a MongoDB Cloud test user

MongoDB Cloud supports just-in-time user provisioning, which is enabled by default. There is no additional action for you to take. If a user doesn't already exist in MongoDB Cloud, a new one is created after authentication.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration by using Access Panel.

When you select the MongoDB Cloud tile in Access Panel, you're automatically signed in to the MongoDB Cloud for which you set up SSO. For more information, see [Sign in and start apps from the My Apps portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Sign up for MongoDB Atlas on Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mongodb_atlas_may_2020?tab=Overview)

- [Try MongoDB Cloud with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Protect MongoDB Cloud with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

