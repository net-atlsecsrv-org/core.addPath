---
title: 'Tutorial: Azure Active Directory integration with Kantega SSO for JIRA | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Kantega SSO for JIRA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Kantega SSO for JIRA

In this tutorial, you learn how to integrate Kantega SSO for JIRA with Azure Active Directory (Azure AD).
Integrating Kantega SSO for JIRA with Azure AD provides you with the following benefits:

* You can control in Azure AD who has access to Kantega SSO for JIRA.
* You can enable your users to be automatically signed-in to Kantega SSO for JIRA (Single Sign-On) with their Azure AD accounts.
* You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [What is application access and single sign-on with Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To configure Azure AD integration with Kantega SSO for JIRA, you need the following items:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get a [free account](https://azure.microsoft.com/free/)
* Kantega SSO for JIRA single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Kantega SSO for JIRA supports **SP and IDP** initiated SSO

## Adding Kantega SSO for JIRA from the gallery

To configure the integration of Kantega SSO for JIRA into Azure AD, you need to add Kantega SSO for JIRA from the gallery to your list of managed SaaS apps.

**To add Kantega SSO for JIRA from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon.

	![The Azure Active Directory button](common/select-azuread.png)

2. Navigate to **Enterprise Applications** and then select the **All Applications** option.

	![The Enterprise applications blade](common/enterprise-applications.png)

3. To add new application, click **New application** button on the top of dialog.

	![The New application button](common/add-new-app.png)

4. In the search box, type **Kantega SSO for JIRA**, select **Kantega SSO for JIRA** from result panel then click **Add** button to add the application.

	![Kantega SSO for JIRA in the results list](common/search-new-app.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with Kantega SSO for JIRA based on a test user called **Britta Simon**.
For single sign-on to work, a link relationship between an Azure AD user and the related user in Kantega SSO for JIRA needs to be established.

To configure and test Azure AD single sign-on with Kantega SSO for JIRA, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Configure Kantega SSO for JIRA Single Sign-On](#configure-kantega-sso-for-jira-single-sign-on)** - to configure the Single Sign-On settings on application side.
3. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Create Kantega SSO for JIRA test user](#create-kantega-sso-for-jira-test-user)** - to have a counterpart of Britta Simon in Kantega SSO for JIRA that is linked to the Azure AD representation of user.
6. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal.

To configure Azure AD single sign-on with Kantega SSO for JIRA, perform the following steps:

1. In the [Azure portal](https://portal.azure.com/), on the **Kantega SSO for JIRA** application integration page, select **Single sign-on**.

    ![Configure single sign-on link](common/select-sso.png)

2. On the **Select a Single sign-on method** dialog, select **SAML/WS-Fed** mode to enable single sign-on.

    ![Single sign-on select mode](common/select-saml-option.png)

3. On the **Set up Single Sign-On with SAML** page, click **Edit** icon to open **Basic SAML Configuration** dialog.

	![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, perform the following steps:

    ![Screenshot that shows the "Basic S A M L Configuration" with the "Identifier" and "Reply U R L" textbox highlighted and the "Save" button selected.](common/idp-intiated.png)

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Click **Set additional URLs** and perform the following step if you wish to configure the application in **SP** initiated mode:

    ![Kantega SSO for JIRA Domain and URLs single sign-on information](common/metadata-upload-additional-signon.png)

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

	> [!NOTE]
	> These values are not real. Update these values with the actual Identifier, Reply URL, and Sign-On URL. These values are received during the configuration of Jira plugin, which is explained later in the tutorial.

6. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

	![The Certificate download link](common/metadataxml.png)

7. On the **Set up Kantega SSO for JIRA** section, copy the appropriate URL(s) as per your requirement.

	![Copy configuration URLs](common/copy-configuration-urls.png)

	a. Login URL

	b. Azure AD Identifier

	c. Logout URL

### Configure Kantega SSO for JIRA Single Sign-On

1. In a different web browser window, sign in to your JIRA on-premises server as an administrator.

1. Hover on cog and click the **Add-ons**.

	![Screenshot that shows the "Cog" icon selected and "Add-ons" selected from the drop-down.](./media/kantegassoforjira-tutorial/addon1.png)

1. Under Add-ons tab section, click **Find new add-ons**. Search **Kantega SSO for JIRA (SAML & Kerberos)** and click **Install** button to install the new SAML plugin.

	![Screenshot that shows the "Find new Add-ons" section with "Kantego S S O for JIRA (S A M L & Kerberos)" in the search box and the "Install" button selected.](./media/kantegassoforjira-tutorial/addon2.png)

1. The plugin installation starts.

	![Screenshot that shows the plugin "Installing" dialog.](./media/kantegassoforjira-tutorial/addon3.png)

1. Once the installation is complete. Click **Close**.

	![Screenshot that shows the "Installed and ready to go!" dialog with the "Close" action selected.](./media/kantegassoforjira-tutorial/addon33.png)

1.	Click **Manage**.

	![Screenshot that shows the "Kantega S S O" app page with the "Manage" button selected.](./media/kantegassoforjira-tutorial/addon34.png)
    
1. New plugin is listed under **INTEGRATIONS**. Click **Configure** to configure the new plugin.

	![Screenshot that shows "INTEGRATIONS" in the left-side navigation menu highlighted and the "Configure" button selected in the "Manage add-ons" section.](./media/kantegassoforjira-tutorial/addon35.png)

1. In the **SAML** section. Select **Azure Active Directory (Azure AD)** from the **Add identity provider** dropdown.

    ![Screenshot that shows the "Add identity provider" drop-down with "Azure Active Directory (Azure A D)" selected.](./media/kantegassoforjira-tutorial/addon4.png)

1. Select subscription level as **Basic**.

    ![Screenshot that shows the "Preparing Azure A D" section with "Basic" selected.](./media/kantegassoforjira-tutorial/addon5.png)

1. On the **App properties** section, perform following steps: 

    ![Screenshot that shows the "App properties" section with the "App I D U R L" textbox and copy button highlighted, and the "Next" button selected.](./media/kantegassoforjira-tutorial/addon6.png)

    1. Copy the **App ID URI** value and use it as **Identifier, Reply URL, and Sign-On URL** on the **Basic SAML Configuration** section in Azure portal.

    1. Click **Next**.

1. On the **Metadata import** section, perform following steps: 

    ![Screenshot that shows the "Metadata import" section with "Metadata file on my computer" selected.](./media/kantegassoforjira-tutorial/addon7.png)

    1. Select **Metadata file on my computer**, and upload metadata file, which you have downloaded from Azure portal.

    1. Click **Next**.

1. On the **Name and SSO location** section, perform following steps:

    ![Screenshot that shows the "Name and S S O location" with the "Identity provider name" textbox highlighted, and the "Next" button selected.](./media/kantegassoforjira-tutorial/addon8.png)

    1. Add Name of the Identity Provider in **Identity provider name** textbox (e.g Azure AD).

    1. Click **Next**.

1. Verify the Signing certificate and click **Next**.

    ![Screenshot that shows the "Signature verification" section with the "Next" button selected.](./media/kantegassoforjira-tutorial/addon9.png)

1. On the **JIRA user accounts** section, perform following steps:

    ![Screenshot that shows the "JIRA user accounts" with the "Create users in JIRA's Internal Directory if needed" option highlighted and the "Next" button selected.](./media/kantegassoforjira-tutorial/addon10.png)

    1. Select **Create users in JIRA's internal Directory if needed** and enter the appropriate name of the group for users (can be multiple no. of groups separated by comma).

    1. Click **Next**.

1. Click **Finish**.

    ![Screenshot that shows the "Summary" section with teh "Finish" button selected.](./media/kantegassoforjira-tutorial/addon11.png)

1. On the **Known domains for Azure AD** section, perform following steps:

    ![Configure Single Sign-On](./media/kantegassoforjira-tutorial/addon12.png)

    1. Select **Known domains** from the left panel of the page.

    2. Enter domain name in the **Known domains** textbox.

    3. Click **Save**.

### Create an Azure AD test user

The objective of this section is to create a test user in the Azure portal called Britta Simon.

1. In the Azure portal, in the left pane, select **Azure Active Directory**, select **Users**, and then select **All users**.

    ![The "Users and groups" and "All users" links](common/users.png)

2. Select **New user** at the top of the screen.

    ![New user Button](common/new-user.png)

3. In the User properties, perform the following steps.

    ![The User dialog box](common/user-properties.png)

    1. In the **Name** field enter **BrittaSimon**.

    1. In the **User name** field type `brittasimon@yourcompanydomain.extension`. For example, BrittaSimon@contoso.com

    1. Select **Show password** check box, and then write down the value that's displayed in the Password box.

    1. Click **Create**.

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to Kantega SSO for JIRA.

1. In the Azure portal, select **Enterprise Applications**, select **All applications**, then select **Kantega SSO for JIRA**.

    ![Enterprise applications blade](common/enterprise-applications.png)

2. In the applications list, select **Kantega SSO for JIRA**.

    ![The Kantega SSO for JIRA link in the Applications list](common/all-applications.png)

3. In the menu on the left, select **Users and groups**.

    ![The "Users and groups" link](common/users-groups-blade.png)

4. Click the **Add user** button, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add Assignment pane](common/add-assign-user.png)

5. In the **Users and groups** dialog select **Britta Simon** in the Users list, then click the **Select** button at the bottom of the screen.

6. If you are expecting any role value in the SAML assertion then in the **Select Role** dialog select the appropriate role for the user from the list, then click the **Select** button at the bottom of the screen.

7. In the **Add Assignment** dialog click the **Assign** button.

### Create Kantega SSO for JIRA test user

To enable Azure AD users to sign in to JIRA, they must be provisioned into JIRA. In Kantega SSO for JIRA, provisioning is a manual task.

**To provision a user account, perform the following steps:**

1. Sign in to your JIRA on-premises server as an administrator.

1. Hover on cog and click the **User management**.

    ![Screenshot that shows the "Cog" icon selected, and "User management" selected from the drop-down.](./media/kantegassoforjira-tutorial/user1.png) 

1. Under **User management** tab section, click **Create user**.

    ![Screenshot that shows the "User management" section with the "Create user" button selected.](./media/kantegassoforjira-tutorial/user2.png) 

1. On the **???Create new user???** dialog page, perform the following steps:

    ![Add Employee](./media/kantegassoforjira-tutorial/user3.png) 

    1. In the **Email address** textbox, type the email address of user like Brittasimon@contoso.com.

    2. In the **Full Name** textbox, type full name of the user like Britta Simon.

    3. In the **Username** textbox, type the email of user like Brittasimon@contoso.com.

    4. In the **Password** textbox, type the password of user.

    5. Click **Create user**.

### Test single sign-on 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the Kantega SSO for JIRA tile in the Access Panel, you should be automatically signed in to the Kantega SSO for JIRA for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md).

## Additional Resources

- [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [What is Conditional Access in Azure Active Directory?](../conditional-access/overview.md)