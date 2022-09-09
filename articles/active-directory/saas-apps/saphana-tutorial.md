---
title: 'Tutorial: Azure Active Directory integration with SAP HANA | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with SAP HANA

In this tutorial, you'll learn how to integrate SAP HANA with Azure Active Directory (Azure AD). When you integrate SAP HANA with Azure AD, you can:

* Control in Azure AD who has access to SAP HANA.
* Enable your users to be automatically signed-in to SAP HANA with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To configure Azure AD integration with SAP HANA, you need the following items:

- An Azure AD subscription
- A SAP HANA subscription that's single sign-on (SSO) enabled
- A HANA instance that's running on any public IaaS, on-premises, Azure VM, or SAP large instances in Azure
- The XSA Administration web interface, as well as HANA Studio installed on the HANA instance

> [!NOTE]
> We do not recommend using a production environment of SAP HANA to test the steps in this tutorial. Test the integration first in the development or staging environment of the application, and then use the production environment.

To test the steps in this tutorial, follow these recommendations:

* An Azure AD subscription. If you don't have an Azure AD environment, you can get one-month trial [here](https://azure.microsoft.com/pricing/free-trial/)
* SAP HANA single sign-on enabled subscription

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* SAP HANA supports **IDP** initiated SSO
* SAP HANA supports **just-in-time** user provisioning

> [!NOTE]
> Identifier of this application is a fixed string value so only one instance can be configured in one tenant.


## Adding SAP HANA from the gallery

To configure the integration of SAP HANA into Azure AD, you need to add SAP HANA from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **SAP HANA** in the search box.
1. Select **SAP HANA** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for SAP HANA

Configure and test Azure AD SSO with SAP HANA using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in SAP HANA.

To configure and test Azure AD SSO with SAP HANA, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
	1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
	1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
2. **[Configure SAP HANA SSO](#configure-sap-hana-sso)** - to configure the Single Sign-On settings on application side.
	1. **[Create SAP HANA test user](#create-sap-hana-test-user)** - to have a counterpart of Britta Simon in SAP HANA that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **SAP HANA** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, enter the values for the following fields:

    In the **Reply URL** text box, type a URL using the following pattern:
    `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

	> [!NOTE]
	> The Reply URL value is not real. Update the value with the actual Reply URL. Contact [SAP HANA Client support team](https://cloudplatform.sap.com/contact.html) to get the values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. SAP HANA application expects the SAML assertions in a specific format. Configure the following claims for this application. You can manage the values of these attributes from the **User Attributes** section on application integration page. On the **Set up Single Sign-On with SAML** page, click **Edit** button to open **User Attributes** dialog.

	![Screenshot that shows the "User Attributes" section with the "Edit" icon selected.](common/edit-attribute.png)

6. In the **User attributes** section on the **User Attributes & Claims** dialog, perform the following steps:
 
	a. Click **Edit icon** to open the **Manage user claims** dialog.

	![Screenshot that shows the "User Attributes & Claims" dialog with the "Edit" icon selected.](./media/saphana-tutorial/tutorial_usermail.png)

	![image](./media/saphana-tutorial/tutorial_usermailedit.png)

	b. From the **Transformation** list, select **ExtractMailPrefix()**.

	c. From the **Parameter 1** list, select **user.mail**.

	d. Click **Save**.

7. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Federation Metadata XML** from the given options as per your requirement and save it on your computer.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to SAP HANA.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **SAP HANA**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure SAP HANA SSO

1. To configure single sign-on on the SAP HANA side, sign in to your **HANA XSA Web Console**  by going to the respective HTTPS endpoint.

	> [!NOTE]
	> In the default configuration, the URL redirects the request to a sign-in screen, which requires the credentials of an authenticated SAP HANA database user. The user who signs in must have permissions to perform SAML administration tasks.

2. In the XSA Web Interface, go to **SAML Identity Provider**. From there, select the **+** button on the bottom of the screen to display the **Add Identity Provider Info** pane. Then take the following steps:

	![Add Identity Provider](./media/saphana-tutorial/sap1.png)

	a. In the **Add Identity Provider Info** pane, paste the contents of the Metadata XML (which you downloaded from the Azure portal) into the **Metadata** box.

	![Screenshot that shows the "Add Identity Provider Info" pane with the "Metadata" and "Name" boxes highlighted.](./media/saphana-tutorial/sap2.png)

	b. If the contents of the XML document are valid, the parsing process extracts the information that's required for the **Subject, Entity ID, and Issuer** fields in the **General data** screen area. It also extracts the information that's necessary for the URL fields in the **Destination** screen area, for example, the **Base URL and SingleSignOn URL (*)** fields.

	![Add Identity Provider settings](./media/saphana-tutorial/sap3.png)

	c. In the **Name** box of the **General Data** screen area, enter a name for the new SAML SSO identity provider.

	> [!NOTE]
	> The name of the SAML IDP is mandatory and must be unique. It appears in the list of available SAML IDPs that is displayed when you select SAML as the authentication method for SAP HANA XS applications to use. For example, you can do this in the **Authentication** screen area of the XS Artifact Administration tool.

3. Select **Save** to save the details of the SAML identity provider and to add the new SAML IDP to the list of known SAML IDPs.

	![Save button](./media/saphana-tutorial/sap4.png)

4. In HANA Studio, within the system properties of the **Configuration** tab,  filter the settings by **saml**. Then adjust the **assertion_timeout** from **10 sec** to **120 sec**.

	![assertion_timeout setting](./media/saphana-tutorial/sap7.png)


### Create SAP HANA test user

To enable Azure AD users to sign in to SAP HANA, you must provision them in SAP HANA.
SAP HANA supports **just-in-time provisioning**, which is by enabled by default.

If you need to create a user manually, take the following steps:

>[!NOTE]
>You can change the external authentication that the user uses. They can authenticate with an external system such as Kerberos. For detailed information about external identities, contact your [domain administrator](https://cloudplatform.sap.com/contact.html).

1. Open the [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) as an administrator, and then enable the DB-User for SAML SSO.

	![Create user](./media/saphana-tutorial/sap5.png)

2. Select the invisible check box to the left of **SAML**, and then select the **Configure** link.

3. Select **Add** to add the SAML IDP.  Select the appropriate SAML IDP, and then select **OK**.

4. Add the **External Identity** (in this case, BrittaSimon) or choose **Any**. Then select **OK**.

   > [!Note]
   > If  the **Any** check box is not selected, then the user name in HANA needs to exactly match the name of the user in the UPN before the domain suffix. (For example, BrittaSimon@contoso.com becomes BrittaSimon in HANA.)

5. For testing purposes, assign all **XS** roles to the user.

	![Assigning roles](./media/saphana-tutorial/sap6.png)

 	> [!TIP]
  	> You should give permissions that are appropriate for your use cases only.

6. Save the user.

### Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options.

* Click on Test this application in Azure portal and you should be automatically signed in to the SAP HANA for which you set up the SSO

* You can use Microsoft My Apps. When you click the SAP HANA tile in the My Apps, you should be automatically signed in to the SAP HANA for which you set up the SSO. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).


## Next steps

Once you configure SAP HANA you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).