---
title: 'Tutorial: Azure Active Directory single sign-on (SSO) integration with Akamai | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
---

# Tutorial: Azure Active Directory single sign-on (SSO) integration with Akamai

In this tutorial, you'll learn how to integrate Akamai with Azure Active Directory (Azure AD). When you integrate Akamai with Azure AD, you can:

* Control in Azure AD who has access to Akamai.
* Enable your users to be automatically signed-in to Akamai with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

Azure Active Directory and Akamai Enterprise Application Access integration allows seamless access to legacy applications hosted in the cloud or on-premises. The integrated solution takes advantages of all the modern capabilities of Azure Active Directory like [Azure AD conditional access](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) and [Azure AD Identity Governance](../governance/identity-governance-overview.md) for legacy applications access without app modifications or agents installation.

The below image describes, where Akamai EAA fits into the broader Hybrid Secure Access scenario.

![Akamai EAA fits into the broader Hybrid Secure Access scenario](./media/header-akamai-tutorial/introduction-1.png)

### Key Authentication Scenarios

Apart from Azure Active Directory native integration support for modern authentication protocols like Open ID Connect, SAML and WS-Fed, Akamai EAA extends secure access for legacy-based authentication apps for both internal and external access with Azure AD, enabling modern scenarios (e.g. password-less access) to these applications. This includes:

* Header-based authentication apps
* Remote Desktop
* SSH (Secure Shell)
* Kerberos authentication apps
* VNC (Virtual Network Computing)
* Anonymous auth or no inbuilt authentication apps
* NTLM authentication apps (protection with dual prompts for the user)
* Forms-Based Application (protection with dual prompts for the user)

### Integration Scenarios

Microsoft and Akamai EAA partnership allows the flexibility to meet your business requirements by supporting multiple integration scenarios based on your business requirement. These could be used to provide zero-day coverage across all applications and gradually classify and configure appropriate policy classifications.

#### Integration Scenario 1

Akamai EAA is configured as a single application on the Azure AD. Admin can configure the Conditional Access policy on the Application and once the conditions are satisfied users can gain access to the Akamai EAA Portal.

**Pros**:

* You need to only configure IDP once.

**Cons**:

* Users end up having two applications portals.

* Single Common Conditional Access policy coverage for all Applications.

![Integration Scenario 1](./media/header-akamai-tutorial/scenario-1.png)

#### Integration Scenario 2

Akamai EAA Application is set up individually on the Azure AD Portal. Admin can configure Individual he Conditional Access policy on the Application(s) and once the conditions are satisfied users can directly be redirected to the specific application.

**Pros**:

* You can define individual CA Policies.

* All Apps are represented on the 0365 Waffle and myApps.microsoft.com Panel.


**Cons**:

* You need to configure multiple IDP.

![Integration Scenario 2](./media/header-akamai-tutorial/scenario-2.png)

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Akamai single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

- Akamai supports IDP initiated SSO.

#### Important

All the setup listed below are same for the **Integration Scenario 1** and **Scenario 2**. For the **Integration scenario 2** you have to set up Individual IDP in the Akamai EAA and the URL property needs to be modified to point to the application URL.

![Screenshot of the General tab for AZURESSO-SP in Akamai Enterprise Application Access. The Authentication configuration URL field is highlighted.](./media/header-akamai-tutorial/important.png)

## Add Akamai from the gallery

To configure the integration of Akamai into Azure AD, you need to add Akamai from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Akamai** in the search box.
1. Select **Akamai** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Akamai

Configure and test Azure AD SSO with Akamai using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Akamai.

To configure and test Azure AD SSO with Akamai, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    * **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    * **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Akamai SSO](#configure-akamai-sso)** - to configure the single sign-on settings on application side.
    * **[Setting up IDP](#setting-up-idp)**
    * **[Header Based Authentication](#header-based-authentication)**
    * **[Remote Desktop](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos Authentication](#kerberos-authentication)**
    * **[Create Akamai test user](#create-akamai-test-user)** - to have a counterpart of B.Simon in Akamai that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Akamai** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, if you wish to configure the application in **IDP** initiated mode, enter the values for the following fields:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. In the **Reply URL** text box, type a URL using the following pattern:
    `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > These values are not real. Update these values with the actual Identifier and Reply URL. Contact [Akamai Client support team](https://www.akamai.com/us/en/contact-us/) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Federation Metadata XML** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/metadataxml.png)

1. On the **Set up Akamai** section, copy the appropriate URL(s) based on your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Akamai.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Akamai**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Akamai SSO

### Setting up IDP

**AKAMAI EAA IDP Configuration**

1. Sign in to **Akamai Enterprise Application Access** console.
1. On the **Akamai EAA console**, Select **Identity** > **Identity Providers** and click **Add Identity Provider**.

    ![Screenshot of the Akamai EAA console Identity Providers window. Select Identity Providers on the Identity menu and select Add Identity Provider.](./media/header-akamai-tutorial/configure-1.png)

1. On the **Create New Identity Provider** perform the following steps:

    ![Screenshot of the Create New Identity Providers dialog in the Akamai EAA console.](./media/header-akamai-tutorial/configure-2.png)

    a. Specify the **Unique Name**.

    b. Choose **Third Party SAML** and click **Create Identity Provider and Configure**.

### General Settings

1. **Identity Intercept** - Specify the name of the (SP base URL–will be used for Azure AD Configuration).

    > [!NOTE]
    > You can choose to have your own custom domain (will require a DNS entry and a Certificate). In this example we are going to use the Akamai Domain.

1. **Akamai Cloud Zone** - Select the Appropriate cloud zone.
1. **Certificate Validation** - Check Akamai Documentation (optional).

    ![Screenshot of the Akamai EAA console General tab showing settings for Identity Intercept, Akamai Cloud Zone, and Certificate Validation.](./media/header-akamai-tutorial/configure-3.png)

### Authentication Configuration

1. URL – Specify the URL same as your identity intercept ( this is where users are redirect after authentication).
2. Logout URL : Update the logout URL.
3. Sign SAML Request: default unchecked.
4. For the IDP Metadata File, add the Application in the Azure AD Console.

    ![Screenshot of the Akamai EAA console Authentication configuration showing settings for URL, Logout URL, Sign SAML Request, and IDP Metadata File.](./media/header-akamai-tutorial/configure-4.png)

### Session Settings

Leave the settings as default.

![Screenshot of the Akamai EAA console Session settings dialog.](./media/header-akamai-tutorial/session-settings.png)

### Directories

Skip the directory configuration.

![Screenshot of the Akamai EAA console Directories tab.](./media/header-akamai-tutorial/directories.png)

### Customization UI

You could add customization to IDP.

![Screenshot of the Akamai EAA console Customization tab showing settings for Customize UI, Language settings, and Themes.](./media/header-akamai-tutorial/customization.png)

### Advanced Settings

Skip Advance settings / refer Akamai documentation for more details.

![Screenshot of the Akamai EAA console Advanced Settings tab showing settings for EAA Client, Advanced, and OIDC to SAML bridging.](./media/header-akamai-tutorial/advance-settings.png)

### Deployment

1. Click on Deploy Identity Provider.

    ![Screenshot of the Akamai EAA console Deployment tab showing the Deploy dentity provider button.](./media/header-akamai-tutorial/deployment.png)

2. Verify the deployment was successful.

### Header Based Authentication

Akamai Header Based Authentication

1. Choose **Custom HTTP** form the Add Applications Wizard.

    ![Screenshot of the Akamai EAA console Add Applications wizard showing CustomHTTP listed in the Access Apps section.](./media/header-akamai-tutorial/configure-5.png)

2. Enter **Application Name** and **Description**.

    ![Screenshot of a Custom HTTP App dialog showing settings for Application Name and Description.](./media/header-akamai-tutorial/configure-6.png)

    ![Screenshot of the Akamai EAA console General tab showing general settings for MYHEADERAPP.](./media/header-akamai-tutorial/configure-7.png)

    ![Screenshot of the Akamai EAA console showing settings for Certificate and Location.](./media/header-akamai-tutorial/configure-8.png)

#### Authentication

1. Select **Authentication** tab.

    ![Screenshot of the Akamai EAA console with the Authentication tab selected.](./media/header-akamai-tutorial/configure-9.png)

2. Assign the **Identity provider**.

    ![Screenshot of the Akamai EAA console Authentication tab for MYHEADERAPP showing the Identity provider set to Azure AD SSO.](./media/header-akamai-tutorial/configure-10.png)

#### Services

Click Save and Go to Authentication.

![Screenshot of the Akamai EAA console Services tab for MYHEADERAPP showing the Save and go to AdvancedSettings button in the bottom right corner.](./media/header-akamai-tutorial/configure-11.png)

#### Advanced Settings

1. Under the **Customer HTTP Headers**, specify the **CustomerHeader** and **SAML Attribute**.

    ![Screenshot of the Akamai EAA console Advanced Settings tab showing the SSO Logged URL field highlighted under Authentication.](./media/header-akamai-tutorial/configure-12.png)

1. Click **Save and go to Deployment** button.

    ![Screenshot of the Akamai EAA console Advanced Settings tab showing the Save and go to Deployment button in the bottom right corner.](./media/header-akamai-tutorial/configure-13.png)

#### Deploy the Application

1. Click **Deploy Application** button.

    ![Screenshot of the Akamai EAA console Deployment tab showing the Deploy application button.](./media/header-akamai-tutorial/configure-14.png)

1. Verify the Application was deployed successfully.

    ![Screenshot of the Akamai EAA console Deployment tab showing the Application status message: "Application Successfully Deployed".](./media/header-akamai-tutorial/configure-15.png)

1. End-User Experience.

    ![Screenshot of the opening screen for myapps.microsoft.com with a background image and a Sign in dialog.](./media/header-akamai-tutorial/end-user-1.png)

    ![Screenshot showing part of an Apps window with icons for Add-in, HRWEB, Akamai - CorpApps, Expense, Groups, and Access reviews. ](./media/header-akamai-tutorial/end-user-2.png)

1. Conditional Access.

    ![Screenshot of the message: Approve sign in request. We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Screenshot of an Applications screen showing an icon for the MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### Remote Desktop

1. Choose **RDP** from the ADD Applications Wizard.

    ![Screenshot of the Akamai EAA console Add Applications wizard showing RDP listed among the apps in the Access Apps section.](./media/header-akamai-tutorial/configure-16.png)

1. Enter **Application Name** and **Description**.

    ![Screenshot of a RDP App dialog showing settings for Application Name and Description.](./media/header-akamai-tutorial/configure-17.png)

    ![Screenshot of the Akamai EAA console General tab showing Application identity settings for SECRETRDPAPP.](./media/header-akamai-tutorial/configure-18.png)

1. Specify the Connector that will be servicing this.

    ![Screenshot of the Akamai EAA console showing settings for Certificate and Location. Associated connectors is set to USWST-CON1.](./media/header-akamai-tutorial/configure-19.png)

#### Authentication

Click **Save and go to Services**.

![Screenshot of the Akamai EAA console Authentication tab for SECRETRDPAPP showing the Save and go to Services button is in the bottom right corner.](./media/header-akamai-tutorial/configure-20.png)

#### Services

Click **Save and go to Advanced Settings**.

![Screenshot of the Akamai EAA console Services tab for SECRETRDPAPP showing the Save and go to AdvancedSettings button in the bottom right corner.](./media/header-akamai-tutorial/configure-21.png)

#### Advanced Settings

1. Click **Save and go to Deployment**.

    ![Screenshot of the Akamai EAA console Advanced Settings tab for SECRETRDPAPP showing the settings for Remote desktop configuration.](./media/header-akamai-tutorial/configure-22.png)

    ![Screenshot of the Akamai EAA console Advanced Settings tab for SECRETRDPAPP showing the settings for Authentication and Health check configuration.](./media/header-akamai-tutorial/configure-23.png)

    ![Screenshot of the Akamai EAA console Custom HTTP headers settings for SECRETRDPAPP with the Save and go to Deployment button in the bottom right corner.](./media/header-akamai-tutorial/configure-24.png)

1. End-User Experience

    ![Screenshot of a myapps.microsoft.com window with a background image and a Sign in dialog.](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot of the myapps.microsoft.com Apps window with icons for Add-in, HRWEB, Akamai - CorpApps, Expense, Groups, and Access reviews.](./media/header-akamai-tutorial/end-user-2.png)

1. Conditional Access

    ![Screenshot of the conditional access message: Approve sign in request. We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot of an Applications screen showing icons for the MyHeaderApp and SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Screenshot of  Windows Server 2012 RS screen showing generic user icons. The icons for administrator, user0, and user1 show that they are Signed in.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Alternatively, you can also directly Type the RDP Application URL.

#### SSH

1. Go to Add Applications, Choose **SSH**.

    ![Screenshot of the Akamai EAA console Add Applications wizard showing SSH listed among the apps in the Access Apps section.](./media/header-akamai-tutorial/configure-25.png)

1. Enter **Application Name** and **Description**.

    ![Screenshot of a SSH App dialog showing settings for Application Name and Description.](./media/header-akamai-tutorial/configure-26.png)

1. Configure Application Identity.

    ![Screenshot of the Akamai EAA console General tab showing Application identity settings for SSH-SECURE.](./media/header-akamai-tutorial/configure-27.png)

    a. Specify Name / Description.

    b. Specify Application Server IP/FQDN and port for SSH.

    c. Specify SSH username / passphrase *Check Akamai EAA.

    d. Specify the External host Name.

    e. Specify the Location for the connector and choose the connector.

#### Authentication

Click on **Save and go to Services**.

![Screenshot of the Akamai EAA console Authentication tab for SSH-SECURE showing the Save and go to Services button is in the bottom right corner.](./media/header-akamai-tutorial/configure-28.png)

#### Services

Click **Save and go to Advanced Settings**.

![Screenshot of the Akamai EAA console Services tab for SSH-SECURE showing the Save and go to AdvancedSettings button in the bottom right corner.](./media/header-akamai-tutorial/configure-29.png)

#### Advanced Settings

Click Save and to go Deployment.

![Screenshot of the Akamai EAA console Advanced Settings tab for SSH-SECURE showing the settings for Authentication and Health check configuration.](./media/header-akamai-tutorial/configure-30.png)

![Screenshot of the Akamai EAA console Custom HTTP headers settings for SSH-SECURE with the Save and go to Deployment button in the bottom right corner.](./media/header-akamai-tutorial/configure-31.png)

#### Deployment

1. Click **Deploy application**.

    ![Screenshot of the Akamai EAA console Deployment tab for SSH-SECURE showing the Deploy application button.](./media/header-akamai-tutorial/configure-32.png)

1. End-User Experience

    ![Screenshot of a myapps.microsoft.com window Sign in dialog.](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot of the Apps window for myapps.microsoft.com showing icons for Add-in, HRWEB, Akamai - CorpApps, Expense, Groups, and Access reviews.](./media/header-akamai-tutorial/end-user-4.png)

1. Conditional Access

    ![Screenshot showing the message: Approve sign in request. We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot of an Applications screen showing icons for MyHeaderApp, SSH Secure, and SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Screenshot of a command window for ssh-secure-go.akamai-access.com showing a Password prompt.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Screenshot of a command window for ssh-secure-go.akamai-access.com showing information about the application and displaying a prompt for commands.](./media/header-akamai-tutorial/conditional-access-9.png)

### Kerberos Authentication

In the below example we will publish an Internal web server <code>http://frp-app1.superdemo.live</code> and enable SSO using KCD.

#### General Tab

![Screenshot of the Akamai EAA console General tab for MYKERBOROSAPP.](./media/header-akamai-tutorial/general-tab.png)

#### Authentication Tab

Assign the Identity Provider.

![Screenshot of the Akamai EAA console Authentication tab for MYKERBOROSAPP showing Identity provider set to Azure AD SSO.](./media/header-akamai-tutorial/authentication-tab.png)

#### Services Tab

![Screenshot of the Akamai EAA console Services tab for MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### Advanced Settings

![Screenshot of the Akamai EAA console Advanced Settings tab for MYKERBOROSAPP showing settings for Related Applications and Authentication.](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> The SPN for the Web Server has be  in SPN@Domain Format ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` for this demo. Leave rest of the settings to default.

#### Deployment Tab

![Screenshot of the Akamai EAA console Deployment tab for MYKERBOROSAPP showing the Deploy application button.](./media/header-akamai-tutorial/deployment-tab.png)

#### Adding Directory

1. Select **AD** from the dropdown.

    ![Screenshot of the Akamai EAA console Directories window showing a Create New Directory dialog with AD selected in the drop down for Directory Type.](./media/header-akamai-tutorial/configure-33.png)

1. Provide the necessary data.

    ![Screenshot of the Akamai EAA console SUPERDEMOLIVE window with settings for DirectoryName, Directory Service, Connector, and Attribute mapping.](./media/header-akamai-tutorial/configure-34.png)

1. Verify the Directory Creation.

    ![Screenshot of the Akamai EAA console Directories window showing that the directory superdemo.live has been added.](./media/header-akamai-tutorial/directory-domain.png)

1. Add the Groups/OUs who would be require access.

    ![Screenshot of the settings for the directory superdemo.live. The icon that you select for adding Groups or OUs is highlighted.](./media/header-akamai-tutorial/add-group.png)

1. In the below the Group is called EAAGroup and has 1 Member.

    ![Screenshot of the Akamai EAA console GROUPS ON SUPERDEMOLIVE DIRECTORY window. The EAAGroup with 1 User is listed under Groups.](./media/header-akamai-tutorial/eaagroup.png)

1. Add the Directory to you Identity Provider by clicking **Identity** > **Identity Providers** and click on the **Directories** Tab and Click on **Assign directory**.

    ![Screenshot of the Akamai EAA console Directories tab for Azure AD SSO, showing superdemo.live in the list of Currently assigned directories.](./media/header-akamai-tutorial/assign-directory.png)

### Configure KCD Delegation for EAA Walkthrough

#### Step 1: Create an Account 

1. In the example we will use an account called **EAADelegation**. You can perform this using the **Active Directory users and computer** Snappin.

    ![Screenshot of the Akamai EAA console Directories tab for Azure AD SSO. The directory superdemo.live is listed under Currently assigned directories.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > The user name has to be in a specific format based on the **Identity Intercept Name**. From the figure 1 we see it is **corpapps.login.go.akamai-access.com**

1. User logon Name will be:`HTTP/corpapps.login.go.akamai-access.com`

    ![Screenshot showing EAADelegation Properties with First name set to "EAADelegation" and User logon name set to HTTP/corpapps.login.go.akamai-access.com.](./media/header-akamai-tutorial/eaadelegation.png)

#### Step 2: Configure the SPN for this account

1. Based on this sample the SPN will be as below.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Screenshot of an Administrator Command Prompt showing the results of the command setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### Step 3: Configure Delegation

1. For the EAADelegation account click on the Delegation tab.

    ![Screenshot of an Administrator Command Prompt showing the command for configuring the SPN.](./media/header-akamai-tutorial/spn.png)

    * Specify use any authentication Protocol.
    * Click Add and Add the App Pool Account for the Kerberos Website. It should automatically resolve to correct SPN if configured correctly.

#### Step 4: Create a Keytab File for AKAMAI EAA

1. Here is the generic Syntax.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Example explained

    | Snippet | Explanation |
    | - | - |
    | Ktpass /out EAADemo.keytab | // Name of the output Keytab file |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA Delegation account |
    | /pass RANDOMPASS | // EAA Delegation account Password |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // consult Akamai EAA documentation |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Screenshot of an Administrator Command Prompt showing the results of the command for creating a Keytab File for AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### Step 5: Import Keytab in the AKAMAI EAA Console

1. Click **System** > **Keytabs**.

    ![Screenshot of the Akamai EAA console showing Keytabs being selected from the System menu.](./media/header-akamai-tutorial/keytabs.png)

1. In the Keytab Type choose **Kerberos Delegation**.

    ![Screenshot of the Akamai EAA console EAAKEYTAB screen showing the Keytab settings. The Keytab Type is set to Kerberos Delegation.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Ensure the Keytab shows up as Deployed and Verified.

    ![Screenshot of the Akamai EAA console KEYTABS screen listing the EAA Keytab as "Keytab deployed and verified".](./media/header-akamai-tutorial/keytabs-2.png)

1. User Experience

    ![Screenshot of the Sign in dialog at myapps.microsoft.com. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot of the Apps window for myapps.microsoft.com showing App icons.](./media/header-akamai-tutorial/end-user-4.png)

1. Conditional Access

    ![Screenshot showing an Approve sign in request message. the message.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot of an Applications screen showing icons for MyHeaderApp, SSH Secure, SecretRDPApp, and myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Screenshot of the splash screen for the myKerberosApp. The message "Welcome superdemo\user1" is displayed over a background image.](./media/header-akamai-tutorial/conditional-access-11.png)

### Create Akamai test user

In this section, you create a user called B.Simon in Akamai. Work with [Akamai Client support team](https://www.akamai.com/us/en/contact-us/) to add the users in the Akamai platform. Users must be created and activated before you use single sign-on. 

## Test SSO

In this section, you test your Azure AD single sign-on configuration with following options.

* Click on Test this application in Azure portal and you should be automatically signed in to the Akamai for which you set up the SSO.

* You can use Microsoft My Apps. When you click the Akamai tile in the My Apps, you should be automatically signed in to the Akamai for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Akamai you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).