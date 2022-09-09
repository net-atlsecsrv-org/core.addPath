---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Lesson.ly | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Lesson.ly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Lesson.ly

In this tutorial, you'll learn how to integrate Lesson.ly with Azure Active Directory (Azure AD). When you integrate Lesson.ly with Azure AD, you can:

* Control in Azure AD who has access to Lesson.ly.
* Enable your users to be automatically signed-in to Lesson.ly with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Lesson.ly single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Lesson.ly supports **SP** initiated SSO
* Lesson.ly supports **Just In Time** user provisioning
* Once you configure Lesson.ly you can enforce session control, which protect exfiltration and infiltration of your organization’s sensitive data in real-time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## Adding Lesson.ly from the gallery

To configure the integration of Lesson.ly into Azure AD, you need to add Lesson.ly from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Lesson.ly** in the search box.
1. Select **Lesson.ly** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD single sign-on for Lesson.ly

Configure and test Azure AD SSO with Lesson.ly using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Lesson.ly.

To configure and test Azure AD SSO with Lesson.ly, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Lesson.ly SSO](#configure-lessonly-sso)** - to configure the single sign-on settings on application side.
    * **[Create Lesson.ly test user](#create-lessonly-test-user)** - to have a counterpart of B.Simon in Lesson.ly that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **Lesson.ly** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<companyname>.lessonly.com/auth/saml`

	> [!NOTE]
	> When referencing a generic name that **companyname** needs to be replaced by an actual name.
	
    b. In the **Reply URL (Assertion Customer Service URL)** text box, type a URL using the following pattern:
    `https://<companyname>.lessonly.com/auth/saml/callback`

    c. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<companyname>.lessonly.com/auth/saml/metadata`
    
	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL, Reply URL, and Identifier. Contact [Lessonly.com Client support team](mailto:support@lessonly.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. Lesson.ly application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes.

	![image](common/default-attributes.png)

1. In addition to above, Lesson.ly application expects few more attributes to be passed back in SAML response which are shown below. These attributes are also pre populated but you can review them as per your requirements.

    | Name | Source Attribute|
	| ---------------  | ----------------|
	| urn:oid:2.5.4.42 | user.givenname |
	| urn:oid:2.5.4.4  | user.surname |
	| urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
	| urn:oid:1.3.6.1.4.1.5923.1.1.1.10 | user.objectid |

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Lesson.ly** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Lesson.ly.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Lesson.ly**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Lesson.ly SSO

To configure single sign-on on **Lesson.ly** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from Azure portal to [Lesson.ly support team](mailto:support@lessonly.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Lesson.ly test user

The objective of this section is to create a user called B.Simon in Lessonly.com. Lessonly.com supports just-in-time provisioning, which is by default enabled.

There is no action item for you in this section. A new user will be created during an attempt to access Lessonly.com if it doesn't exist yet.

> [!NOTE]
> If you need to create an user manually, you need to contact the [Lessonly.com support team](mailto:support@lessonly.com).

## Test SSO

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Lesson.ly tile in the Access Panel, you should be automatically signed in to the Lesson.ly for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try Lesson.ly with Azure AD](https://aad.portal.azure.com/)