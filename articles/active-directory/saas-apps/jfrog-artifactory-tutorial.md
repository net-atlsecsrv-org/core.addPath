---
title: 'Tutorial: Azure Active Directory integration with JFrog Artifactory | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and JFrog Artifactory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess

ms.assetid: 767cb0fc-048c-412b-a8ad-fe52daeeb02d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes

ms.collection: M365-identity-device-management
---

# Tutorial: Integrate JFrog Artifactory with Azure Active Directory

In this tutorial, you'll learn how to integrate JFrog Artifactory with Azure Active Directory (Azure AD). When you integrate JFrog Artifactory with Azure AD, you can:

* Control in Azure AD who has access to JFrog Artifactory.
* Enable your users to be automatically signed-in to JFrog Artifactory with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

To learn more about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* JFrog Artifactory single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* JFrog Artifactory supports **SP and IDP** initiated SSO
* JFrog Artifactory supports **Just In Time** user provisioning

## Adding JFrog Artifactory from the gallery

To configure the integration of JFrog Artifactory into Azure AD, you need to add JFrog Artifactory from the gallery to your list of managed SaaS apps.

1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **JFrog Artifactory** in the search box.
1. Select **JFrog Artifactory** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.


## Configure and test Azure AD single sign-on

Configure and test Azure AD SSO with JFrog Artifactory using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in JFrog Artifactory.

To configure and test Azure AD SSO with JFrog Artifactory, complete the following building blocks:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
2. **[Configure JFrog Artifactory SSO](#configure-jfrog-artifactory-sso)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
5. **[Create JFrog Artifactory test user](#create-jfrog-artifactory-test-user)** - to have a counterpart of B.Simon in JFrog Artifactory that is linked to the Azure AD representation of user.
6. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the [Azure portal](https://portal.azure.com/), on the **JFrog Artifactory** application integration page, find the **Manage** section and select **Single sign-on**.
1. On the **Select a Single sign-on method** page, select **SAML**.
1. On the **Set up Single Sign-On with SAML** page, click the edit/pen icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `<servername>.jfrog.io`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<servername>.jfrog.io/<servername>/webapp/`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL and Sign-on URL. Contact [JFrog Artifactory Client support team](https://support.jfrog.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. JFrog Artifactory application expects the SAML assertions in a specific format, which requires you to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the list of default attributes. Click **Edit** icon to open User Attributes dialog.

	![image](common/edit-attribute.png)

1. In addition to above, JFrog Artifactory application expects few more attributes to be passed back in SAML response. In the **User Attributes & Claims** section on the **Group Claims (Preview)** dialog, perform the following steps:

	a. Click the **pen** next to **Groups returned in claim**.

	![image](./media/jfrog-artifactory-tutorial/config04.png)

	![image](./media/jfrog-artifactory-tutorial/config05.png)

	b. Select **All Groups** from the radio list.

	c. Click **Save**.

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Raw)** and select **Download** to download the certificate and save it on your computer.

	![The Certificate download link](common/certificateraw.png)

6. On the **Set up JFrog Artifactory** section, copy the appropriate URL(s) based on your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

### Configure JFrog Artifactory SSO

To configure single sign-on on **JFrog Artifactory** side, you need to send the downloaded **Certificate (Raw)** and appropriate copied URLs from Azure portal to [JFrog Artifactory support team](https://support.jfrog.com). They set this setting to have the SAML SSO connection set properly on both sides.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to JFrog Artifactory.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **JFrog Artifactory**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

	![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

### Create JFrog Artifactory test user

In this section, a user called B.Simon is created in JFrog Artifactory. JFrog Artifactory supports just-in-time user provisioning, which is enabled by default. There is no action item for you in this section. If a user doesn't already exist in JFrog Artifactory, a new one is created after authentication.

### Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the JFrog Artifactory tile in the Access Panel, you should be automatically signed in to the JFrog Artifactory for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

