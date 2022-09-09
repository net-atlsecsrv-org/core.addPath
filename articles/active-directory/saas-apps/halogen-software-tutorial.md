---
title: 'Tutorial: Azure Active Directory integration with Saba TalentSpace | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Saba TalentSpace

In this tutorial, you'll learn how to integrate Saba TalentSpace with Azure Active Directory (Azure AD). When you integrate Saba TalentSpace with Azure AD, you can:

* Control in Azure AD who has access to Saba TalentSpace.
* Enable your users to be automatically signed-in to Saba TalentSpace with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Saba TalentSpace single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Saba TalentSpace supports **SP** initiated SSO

## Add Saba TalentSpace from the gallery

To configure the integration of Saba TalentSpace into Azure AD, you need to add Saba TalentSpace from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Saba TalentSpace** in the search box.
1. Select **Saba TalentSpace** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Saba TalentSpace

Configure and test Azure AD SSO with Saba TalentSpace using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Saba TalentSpace.

To configure and test Azure AD SSO with Saba TalentSpace, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Saba TalentSpace SSO](#configure-saba-talentspace-sso)** - to configure the single sign-on settings on application side.
    * **[Create Saba TalentSpace test user](#create-saba-talentspace-test-user)** - to have a counterpart of B.Simon in Saba TalentSpace that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Saba TalentSpace** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Sign on URL** text box, type the URL using the following pattern:
    `https://global.hgncloud.com/[companyname]/saml/login`

	b. In the **Identifier (Entity ID)** text box, type the URL using the following pattern:
    `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. In the **Reply URL (Assertion Consumer Service URL)** text box, type the URL using the following pattern:
    `https://global.hgncloud.com/[companyname]/saml/SSO`

	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [Saba TalentSpace Client support team](https://support.saba.com/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

1. On the **Set up Saba TalentSpace** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Saba TalentSpace.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Saba TalentSpace**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Saba TalentSpace SSO

1. In a different browser window, sign-on to your **Saba TalentSpace** application as an administrator.

2. Click the **Options** tab.
  
    ![Screenshot that shows the "saba TalentSpace" home page with the "Options" tab selected.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. In the left navigation pane, click **SAML Configuration**.
  
    ![Screenshot that shows the "User Interface" left navigation pane with "S A M L Configuration" selected.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. On the **SAML Configuration** page, perform the following steps:

    ![Screenshot that shows the "S A M L Configuration" page with the "Settings" options highlighted.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. As **Unique Identifier**, select **NameID**.

    b. As **Unique Identifier Maps To**, select **Username**.
  
    c. To upload your downloaded metadata file, click **Browse** to select the file, and then **Upload File**.

    d. To test the configuration, click **Run Test**.

    > [!NOTE]
    > You need to wait for the message "*The SAML test is complete. Please close this window*". Then, close the opened browser window. The **Enable SAML** checkbox is only enabled if the test has been completed.

    e. Select **Enable SAML**.

    f. Click **Save Changes**.

### Create Saba TalentSpace test user

The objective of this section is to create a user called Britta Simon in Saba TalentSpace.

**To create a user called Britta Simon in Saba TalentSpace, perform the following steps:**

1. Sign on to your **Saba TalentSpace** application as an administrator.

2. Click the **User Center** tab, and then click **Create User**.

    ![Screenshot that shows the "User Center" tab and "Create User" selected.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. On the **New User** dialog page, perform the following steps:

    ![What is Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. In the **First Name** textbox, type first name of the user like **B**.

    b. In the **Last Name** textbox, type last name of the user like **Simon**.

    c. In the **Username** textbox, type **B.Simon**, the user name as in the Azure portal.

    d. In the **Password** textbox, type a password for B.Simon.

    e. Click **Save**.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Saba TalentSpace Sign-on URL where you can initiate the login flow. 

* Go to Saba TalentSpace Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Saba TalentSpace tile in the My Apps, you should be automatically signed in to the Saba TalentSpace for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

 Once you configure Saba TalentSpace you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).