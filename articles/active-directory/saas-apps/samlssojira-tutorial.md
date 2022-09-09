---
title: 'Tutorial: Azure Active Directory integration with SAML SSO for Jira by Resolution GmbH | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and SAML SSO for Jira by resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with SAML SSO for Jira by resolution GmbH

In this tutorial, you'll learn how to integrate SAML SSO for Jira by resolution GmbH with Azure Active Directory (Azure AD). When you integrate SAML SSO for Jira by resolution GmbH with Azure AD, you can:

* Control in Azure AD who has access to SAML SSO for Jira by resolution GmbH.
* Enable your users to be automatically signed-in to SAML SSO for Jira by resolution GmbH with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* SAML SSO for Jira by resolution GmbH single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* SAML SSO for Jira by resolution GmbH supports **SP** and **IDP** initiated SSO.

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.

## Add SAML SSO for Jira by resolution GmbH from the gallery

To configure the integration of SAML SSO for Jira by resolution GmbH into Azure AD, you need to add SAML SSO for Jira by resolution GmbH from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **SAML SSO for Jira by resolution GmbH** in the search box.
1. Select **SAML SSO for Jira by resolution GmbH** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for SAML SSO for Jira by resolution GmbH

Configure and test Azure AD SSO with SAML SSO for Jira by resolution GmbH using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in SAML SSO for Jira by resolution GmbH.

To configure and test Azure AD SSO with SAML SSO for Jira by resolution GmbH, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure SAML SSO for Jira by resolution GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** - to configure the single sign-on settings on application side.
    1. **[Create SAML SSO for Jira by resolution GmbH test user](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** - to have a counterpart of B.Simon in SAML SSO for Jira by resolution GmbH that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **SAML SSO for Jira by resolution GmbH** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. In the **Basic SAML Configuration** section, if you wish to configure the application in the **IDP** initiated mode, then perform the following steps:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/samlsso`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/samlsso`

    c. Click **Set additional URLs** and perform the following step, if you wish to configure the application in the **SP** initiated mode:

    In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
	> For the Identifier, Reply URL and Sign-on URL,  substitute **\<server-base-url>** with the base URL of your Jira instance. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal. If you have a problem, contact us at [SAML SSO for Jira by resolution GmbH Client support team](https://www.resolution.de/go/support).

4. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, download the **Federation Metadata XML** and save it to your computer.

	![The Certificate download link](common/metadataxml.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to SAML SSO for Jira by resolution GmbH.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **SAML SSO for Jira by resolution GmbH**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure SAML SSO for Jira by resolution GmbH SSO 

1. In a different web browser window, sign in to your Jira instance as an administrator.

2. Hover over the cog at the right side and click **Manage apps**.
    
	![Screenshot that shows an arrow pointing at the "Cog" icon, and "Manage apps" selected from the drop-down.](./media/samlssojira-tutorial/add-on-1.png)

3. If you are redirected to Administrator Access page, enter the **Password** and click the **Confirm** button.

	![Screenshot that shows the "Administrator Access" page.](./media/samlssojira-tutorial/add-on-2.png)

4. Jira normally redirects you to the Atlassian marketplace. If not, click on **Find new apps** in the left panel. Search for **SAML Single Sign On (SSO) for JIRA** and click the **Install** button to install the SAML plugin.

	![Screenshot that shows the "Atlassian Marketplace for JIRA" page with an arrow pointing at the "Install" button for the "S A M L Single Sign On (S S O) Jira, S A M L/S S O" app.](./media/samlssojira-tutorial/store.png)

5. The plugin installation will start. When it's done, click the **Close** button.

	![Screenshot that shows the "Installing" dialog.](./media/samlssojira-tutorial/store-2.png)

	![Screenshot that shows the "Installed and ready to go!" dialog with the "Close" button selected.](./media/samlssojira-tutorial/store-3.png)

6. Then, click **Manage**.

	![Screenshot that shows the "S A M L Single Sign On (S S O) Jira, S A M L/S S O" app with the "Manage" button selected.](./media/samlssojira-tutorial/store-4.png)
    
7. Afterwards, click **Configure** to configure the just installed plugin.

	![Screenshot that shows the "Manage apps" page, with the "Configure" button selected for the "S A M L SingleSignOn for Jira" app.](./media/samlssojira-tutorial/store-5.png)

8. In the **SAML SingleSignOn Plugin Configuration** wizard, click **Add new IdP** to configure Azure AD as a new Identity Provider.

	![Screenshot shows the "Welcome" page, with the "Add new I d P" button selected.](./media/samlssojira-tutorial/add-on-4.png) 

9. On the **Choose your SAML Identity Provider** page, perform the following steps:

	![Screenshot that shows the "Choose your S A M L Identity Provider" page with the "I d P Type" and "Name" text boxes highlighted, and the "Next" button selected.](./media/samlssojira-tutorial/identity-provider.png)
 
	a. Set **Azure AD** as the IdP type.
	
	b. Add the **Name** of the Identity Provider (e.g Azure AD).
	
	c. Add an (optional) **Description** of the Identity Provider (e.g Azure AD).
	
	d. Click **Next**.
	
10. On the **Identity provider configuration** page, click **Next**.
 
	![Screenshot that shows the "Identity provider configuration" page.](./media/samlssojira-tutorial/configuration.png)

11. On **Import SAML IdP Metadata** page, perform the following steps:

	![Screenshot that shows the "Import S A M L I d P Metadata" page with the "Select Metadata X M L File" action selected.](./media/samlssojira-tutorial/metadata.png)

    a. Click the **Select Metadata XML File** button and pick the **Federation Metadata XML** file you downloaded before.

    b. Click the **Import** button.
     
    c. Wait briefly until the import succeeds.  
     
    d. Click the **Next** button.
    
12. On **User ID attribute and transformation** page, click the **Next** button.

	![Screenshot that shows the "User I D attribute and transformation" page with the "Next" button selected.](./media/samlssojira-tutorial/transformation.png)
	
13. On the **User creation and update** page, click **Save & Next** to save the settings.
	
	![Screenshot that shows the "User creation and update" page with the "Save & Next" button selected.](./media/samlssojira-tutorial/update.png)
	
14. On the **Test your settings** page, click **Skip test & configure manually** to skip the user test for now. This will be performed in the next section and requires some settings in the Azure portal.
	
	![Screenshot that shows the "Test your settings" page with the "Skip test & configure manually" button selected.](./media/samlssojira-tutorial/test.png)
	
15. Click **OK** to skip the warning.
	
	![Screenshot that shows the warning dialog with the "O K" button selected.](./media/samlssojira-tutorial/warning.png)

### Create SAML SSO for Jira by resolution GmbH test user

To enable Azure AD users to sign in to SAML SSO for Jira by resolution GmbH, they must be provisioned into SAML SSO for Jira by resolution GmbH. For the case of this tutorial, you have to do the provisioning by hand. However, there are also other provisioning models available for the SAML SSO plugin by resolution, for example **Just In Time** provisioning. Refer to their documentation at [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). If you have a question about it, contact support at [resolution support](https://www.resolution.de/go/support).

**To manually provision a user account, perform the following steps:**

1. Sign in to Jira instance as an administrator.

2. Hover over the cog and select **User management**.

   ![Screenshot that shows an arrow pointing at the "Cog" icon with "User management" selected from the drop-down.](./media/samlssojira-tutorial/user-1.png)

3. If you are redirected to the Administrator Access page, then enter the **Password** and click the **Confirm** button.

	![Screenshot that shows the "Administrator Access" page with the "Password" textbox highlighted.](./media/samlssojira-tutorial/user-2.png) 

4. Under the **User management** tab section, click **create user**.

	![Screenshot that shows the "User management" tab with the "Create user" button selected.](./media/samlssojira-tutorial/user-3-new.png) 

5. On the **“Create new user”** dialog page, perform the following steps. You have to create the user exactly like in Azure AD:

	![Add Employee](./media/samlssojira-tutorial/user-4-new.png) 

	a. In the **Email address** textbox, type the email address of the user:  <b>BrittaSimon@contoso.com</b>.

	b. In the **Full Name** textbox, type full name of the user: **Britta Simon**.

	c. In the **Username** textbox, type the email address of the user: <b>BrittaSimon@contoso.com</b>. 

	d. In the **Password** textbox, enter the password of the user.

	e. Click **Create user** to finish the user creation.

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to SAML SSO for Jira by resolution GmbH Sign on URL where you can initiate the login flow.  

* Go to SAML SSO for Jira by resolution GmbH Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the SAML SSO for Jira by resolution GmbH for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the SAML SSO for Jira by resolution GmbH tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the SAML SSO for Jira by resolution GmbH for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Enable SSO redirection for Jira

As noted in the section before, there are currently two ways to trigger the single sign-on. Either by using the **Azure portal** or using **a special link to your Jira instance**. The SAML SSO plugin by resolution GmbH also allows you to trigger single sign-on by simply **accessing any URL pointing to your Jira instance**.

In essence, all users accessing Jira will be redirected to the single sign-on after activating an option in the plugin.

To activate SSO redirect, do the following in **your Jira instance**:

1. Access the configuration page of the SAML SSO plugin in Jira.
1. Click on **Redirection** in the left panel.

   ![Partial screenshot of the Jira SAML SingleSignOn Plugin Configuration page highlighting the Redirection link in the left navigation.](./media/samlssojira-tutorial/configure-1.png)

1. Tick **Enable SSO Redirect**.

   ![Partial screenshot of the Jira SAML SingleSignOn Plugin Configuration page highlighting the selected "Enable SSO Redirect" check box.](./media/samlssojira-tutorial/configure-2.png) 

1. Press the **Save Settings** button in the top right corner.

After activating the option, you can still reach the username/password prompt if the **Enable nosso** option is ticked by navigating to `https://<server-base-url>/login.jsp?nosso`. As always, substitute **\<server-base-url>** with your base URL.

## Next steps

Once you configure SAML SSO for Jira by resolution GmbH you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).