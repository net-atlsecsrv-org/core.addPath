---
title: 'Tutorial: Azure Active Directory integration with Pega Systems | Microsoft Docs'
description: In this tutorial, you'll learn how to configure single sign-on between Azure Active Directory and Pega Systems.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Pega Systems

In this tutorial, you'll learn how to integrate Pega Systems with Azure Active Directory (Azure AD). When you integrate Pega Systems with Azure AD, you can:

* Control in Azure AD who has access to Pega Systems.
* Enable your users to be automatically signed-in to Pega Systems with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Pega Systems single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you'll configure and test Azure AD single sign-on in a test environment.

* Pega Systems supports SP-initiated and IdP-initiated SSO.

## Add Pega Systems from the gallery

To configure the integration of Pega Systems into Azure AD, you need to add Pega Systems from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Pega Systems** in the search box.
1. Select **Pega Systems** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Pega Systems

Configure and test Azure AD SSO with Pega Systems using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Pega Systems.

To configure and test Azure AD SSO with Pega Systems, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Pega Systems SSO](#configure-pega-systems-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Pega Systems test user](#create-pega-systems-test-user)** - to have a counterpart of B.Simon in Pega Systems that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Pega Systems** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. In the **Basic SAML Configuration** dialog box, if you want to configure the application in IdP-initiated mode, complete the following steps.

    ![Basic SAML Configuration dialog box](common/idp-intiated.png)

    1. In the **Identifier** box, enter a URL in this pattern:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. In the **Reply URL** box, enter a URL in this pattern:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. If you want to configure the application in SP-initiated mode, select **Set additional URLs** and complete the following steps.

    ![Pega Systems Domain and URLs single sign-on information](common/both-advanced-urls.png)

	1. In the **Sign on URL** box, enter the sign on URL value.

    1. In the **Relay State** box, enter a URL in this pattern:
       `https://<customername>.pegacloud.io/prweb/sso`

	> [!NOTE]
	> The values provided here are placeholders. You need to use the actual identifier, reply URL, sign on URL, and relay state URL. You can get the identifier and reply URL values from a Pega application, as explained later in this tutorial. To get the relay state value, contact the [Pega Systems support team](https://www.pega.com/contact-us). You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

6. The Pega Systems application needs the SAML assertions to be in a specific format. To get them in the correct format, you need to add custom attribute mappings to your SAML token attributes configuration. The following screenshot shows the default attributes. Select the **Edit** icon to open the **User Attributes** dialog box:

	![User Attributes](common/edit-attribute.png)

7. In addition to the attributes shown in the previous screenshot, the Pega Systems application requires a few more attributes to be passed back in the SAML response. In the **User claims** section of the **User Attributes** dialog box, complete the following steps to add these SAML token attributes:

	
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

	> [!NOTE]
	> These values are specific to your organization. Provide the appropriate values.

	1. Select **Add new claim** to open the **Manage user claims** dialog box:

	![Select Add new claim](common/new-save-attribute.png)

	![Manage user claims dialog box](common/new-attribute-details.png)

	1. In the **Name** box, enter the attribute name shown for that row.

	1. Leave the **Namespace** box empty.

	1. For the **Source**, select **Attribute**.

	1. In the **Source attribute** list, select the attribute value shown for that row.

	1. Select **Ok**.

	1. Select **Save**.

8. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, select the **Download** link next to **Federation Metadata XML**, per your requirements, and save the certificate on your computer:

	![Certificate download link](common/metadataxml.png)

9. In the **Set up Pega Systems** section, copy the appropriate URLs, based on your requirements.

	![Copy the configuration URLs](common/copy-configuration-urls.png)

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Pega Systems.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Pega Systems**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

### Configure Pega Systems SSO

1. To configure single sign-on on the **Pega Systems** side, sign in to the Pega Portal with an admin account in another browser window.

2. Select **Create** > **SysAdmin** > **Authentication Service**:

	![Select Authentication Service](./media/pegasystems-tutorial/admin.png)
	
3. Complete the following steps on the **Create Authentication Service** screen.

	![Create Authentication Service screen](./media/pegasystems-tutorial/admin1.png)

	1. In the **Type** list, select **SAML 2.0**.

	1. In the **Name** box, enter any name (for example, **Azure AD SSO**).

	1. In the **Short description** box, enter a description.  

	1. Select **Create and open**.
	
4. In the **Identity Provider (IdP) information** section, select **Import IdP metadata** and browse to the metadata file that you downloaded from the Azure portal. Click **Submit** to load the metadata:

	![Identity Provider (IdP) information section](./media/pegasystems-tutorial/admin2.png)
	
    The import will populate the IdP data as shown here:

	![Imported IdP data](./media/pegasystems-tutorial/idp.png)
	
6. Complete the following steps in the **Service Provider (SP) settings** section.

	![Service provider settings](./media/pegasystems-tutorial/sp.png)

	1. Copy the **Entity Identification** value and paste it into the **Identifier** box in the **Basic SAML Configuration** section in the Azure portal.

	1. Copy the **Assertion Consumer Service (ACS) location** value and paste it into the **Reply URL** box in the **Basic SAML Configuration** section in the Azure portal.

	1. Select **Disable request signing**.

7. Select **Save**.

### Create Pega Systems test user

Next, you need to create a user named Britta Simon in Pega Systems. Work with the [Pega Systems support team](https://www.pega.com/contact-us) to create users.

### Test SSO

In this section, you test your Azure AD single sign-on configuration with following options. 

#### SP initiated:

* Click on **Test this application** in Azure portal. This will redirect to Pega Systems Sign on URL where you can initiate the login flow.  

* Go to Pega Systems Sign-on URL directly and initiate the login flow from there.

#### IDP initiated:

* Click on **Test this application** in Azure portal and you should be automatically signed in to the Pega Systems for which you set up the SSO. 

You can also use Microsoft My Apps to test the application in any mode. When you click the Pega Systems tile in the My Apps, if configured in SP mode you would be redirected to the application sign on page for initiating the login flow and if configured in IDP mode, you should be automatically signed in to the Pega Systems for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Pega Systems you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).