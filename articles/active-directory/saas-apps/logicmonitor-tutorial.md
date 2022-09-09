---
title: 'Tutorial: Azure Active Directory integration with LogicMonitor | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with LogicMonitor

In this tutorial, you'll learn how to integrate LogicMonitor with Azure Active Directory (Azure AD). When you integrate LogicMonitor with Azure AD, you can:

* Control in Azure AD who has access to LogicMonitor.
* Enable your users to be automatically signed-in to LogicMonitor with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To configure Azure AD integration with LogicMonitor, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/).
* LogicMonitor single sign-on enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* LogicMonitor supports **SP** initiated SSO

## Add LogicMonitor from the gallery

To configure the integration of LogicMonitor into Azure AD, you need to add LogicMonitor from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **LogicMonitor** in the search box.
1. Select **LogicMonitor** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for LogicMonitor

Configure and test Azure AD SSO with LogicMonitor using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in LogicMonitor.

To configure and test Azure AD SSO with LogicMonitor, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure LogicMonitor SSO](#configure-logicmonitor-sso)** - to configure the single sign-on settings on application side.
    1. **[Create LogicMonitor test user](#create-logicmonitor-test-user)** - to have a counterpart of B.Simon in LogicMonitor that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **LogicMonitor** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![LogicMonitor Domain and URLs single sign-on information](common/sp-identifier.png)

	a. In the **Sign on URL** text box, type a URL using the following pattern:
    `https://<companyname>.logicmonitor.com`

    b. In the **Identifier (Entity ID)** text box, type a URL using the following pattern:
    `https://<companyname>.logicmonitor.com`
    
    c. In the **Reply URL (Assertion Consumer Service URL)** textbox, type a URL using the following pattern:
    `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
	> [!NOTE]
	> These values are not real. Update these values with the actual Sign on URL and Identifier. Contact [LogicMonitor Client support team](https://www.logicmonitor.com/contact/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

6. On the **Set up LogicMonitor** section, copy the appropriate URL(s) as per your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to LogicMonitor.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **LogicMonitor**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

### Configure LogicMonitor SSO

1. Log in to your **LogicMonitor** company site as an administrator.

2. In the menu on the top, click **Settings**.

    ![Settings](./media/logicmonitor-tutorial/ic790052.png "Settings")

3. In the navigation bat on the left side, click **Single Sign On**.

    ![Single Sign-On](./media/logicmonitor-tutorial/ic790053.png "Single Sign-On")

4. In the **Single Sign-on (SSO) settings** section, perform the following steps:

    ![Single Sign-On Settings](./media/logicmonitor-tutorial/ic790054.png "Single Sign-On Settings")

    a. Select **Enable Single Sign-on**.

    b. As **Default Role Assignment**, select **readonly**.

    c. Open the downloaded metadata file in notepad, and then paste content of the file into the **Identity Provider Metadata** textbox.

    d. Click **Save Changes**.

### Create LogicMonitor test user

For Azure AD users to be able to sign in, they must be provisioned to the LogicMonitor application using their Azure Active Directory user names.

**To configure user provisioning, perform the following steps:**

1. Log in to your LogicMonitor company site as an administrator.

2. In the menu on the top, click **Settings**, and then click **Roles and Users**.

    ![Roles and Users](./media/logicmonitor-tutorial/ic790056.png "Roles and Users")

3. Click **Add**.

4. In the **Add an account** section, perform the following steps:

    ![Add an account](./media/logicmonitor-tutorial/ic790057.png "Add an account")

    a. Type the **Username**, **Email**, **Password**, and **Retype password** values of the Azure Active Directory user you want to provision into the related textboxes.

    b. Select **Roles**, **View Permissions**, and the **Status**.

    c. Click **Submit**.

> [!NOTE]
> You can use any other LogicMonitor user account creation tools or APIs provided by LogicMonitor to provision Azure Active Directory user accounts.

### Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to LogicMonitor Sign-on URL where you can initiate the login flow. 

* Go to LogicMonitor Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the LogicMonitor tile in the My Apps, you should be automatically signed in to the LogicMonitor for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure LogicMonitor you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).