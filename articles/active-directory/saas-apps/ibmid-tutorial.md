---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with IBMid | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and IBMid.
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

# Tutorial: Azure Active Directory single sign-on (SSO) integration with IBMid

In this tutorial, you'll learn how to integrate IBMid with Azure Active Directory (Azure AD). When you integrate IBMid with Azure AD, you can:

* Control in Azure AD who has access to IBMid.
* Enable your users to be automatically signed-in to IBMid with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* IBMid single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* IBMid supports **SP and IDP** initiated SSO
* IBMid supports **Just In Time** user provisioning

## Adding IBMid from the gallery

To configure the integration of IBMid into Azure AD, you need to add IBMid from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **IBMid** in the search box.
1. Select **IBMid** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD SSO for IBMid

Configure and test Azure AD SSO with IBMid using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in IBMid.

To configure and test Azure AD SSO with IBMid, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure IBMid SSO](#configure-ibmid-sso)** - to configure the single sign-on settings on application side.
    1. **[Create IBMid test user](#create-ibmid-test-user)** - to have a counterpart of B.Simon in IBMid that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **IBMid** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type one of the following URLs:

    | Identifier |
    | ---------- |
    | `https://idaas.iam.ibm.com/idaas/mtfim/sps/idaas/saml20` |
    | `https://ibmlogin.ice.ibmcloud.com/saml/sps/saml20sp/saml20` |
    | `https://prepiam.ice.ibmcloud.com/saml/sps/saml20sp/saml20` |
    |

    a. In the **Reply URL** text box, type one of the following URLs:

    | Reply URL |
    | ---------- |
    | `https://idaas.iam.ibm.com/idaas/mtfim/sps/idaas/saml20/login` |
    | `https://login.ibm.com/saml/sps/saml20sp/saml20/login` |
    | `https://prepiam.ice.ibmcloud.com/saml/sps/saml20sp/saml20/login` |
    |

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type the URL:
    `https://myibm.ibm.com/`


1. Click **Save**.

1. IBMid application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![image](common/default-attributes.png)

1. In addition to above, IBMid application expects few more attributes to be passed back in SAML response, which are shown below. These attributes are also pre populated but you can review them as per your requirements.
	
	| Name |  Source Attribute|
	| ----------- | --------- |
	| country | user.country |
	| firstName | user.givenname |
	| lastName | user.surname |
    | emailAddress | user.mail |


1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

1. On the **Set up IBMid** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to IBMid.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **IBMid**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure IBMid SSO

To configure single sign-on on **IBMid** side, you need to send the downloaded **Federation Metadata XML** and appropriate copied URLs from Azure portal to [IBMid support team](mailto:ibmidfd@us.ibm.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create IBMid test user

In this section, a user called Britta Simon is created in IBMid. IBMid supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in IBMid, a new one is created after authentication.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to IBMid Sign on URL where you can initiate the login flow.  

* Go to IBMid Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the IBMid for which you set up the SSO 

You can also use Microsoft My Apps to test the application in any mode. When you click the IBMid tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the IBMid for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure IBMid you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).