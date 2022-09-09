---
title: 'Tutorial: Azure Active Directory integration with Ceridian Dayforce HCM | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
---
# Tutorial: Azure Active Directory integration with Ceridian Dayforce HCM

In this tutorial, you'll learn how to integrate Ceridian Dayforce HCM with Azure Active Directory (Azure AD). When you integrate Ceridian Dayforce HCM with Azure AD, you can:

* Control in Azure AD who has access to Ceridian Dayforce HCM.
* Enable your users to be automatically signed-in to Ceridian Dayforce HCM with their Azure AD accounts.
* Manage your accounts in one central location - the Azure portal.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Ceridian Dayforce HCM single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD single sign-on in a test environment.

* Ceridian Dayforce HCM supports **SP** initiated SSO

## Add Ceridian Dayforce HCM from the gallery

To configure the integration of Ceridian Dayforce HCM into Azure AD, you need to add Ceridian Dayforce HCM from the gallery to your list of managed SaaS apps.

1. Sign in to the Azure portal using either a work or school account, or a personal Microsoft account.
1. On the left navigation pane, select the **Azure Active Directory** service.
1. Navigate to **Enterprise Applications** and then select **All Applications**.
1. To add new application, select **New application**.
1. In the **Add from the gallery** section, type **Ceridian Dayforce HCM** in the search box.
1. Select **Ceridian Dayforce HCM** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

## Configure and test Azure AD SSO for Ceridian Dayforce HCM

Configure and test Azure AD SSO with Ceridian Dayforce HCM using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Ceridian Dayforce HCM.

To configure and test Azure AD SSO with Ceridian Dayforce HCM, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Ceridian Dayforce HCM SSO](#configure-ceridian-dayforce-hcm-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Ceridian Dayforce HCM test user](#create-ceridian-dayforce-hcm-test-user)** - to have a counterpart of B.Simon in Ceridian Dayforce HCM that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

### Configure Azure AD SSO 

Follow these steps to enable Azure AD SSO in the Azure portal.

1. In the Azure portal, on the **Ceridian Dayforce HCM** application integration page, find the **Manage** section and select **single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

4. On the **Basic SAML Configuration** section, perform the following steps:

    ![Ceridian Dayforce HCM Domain and URLs single sign-on information](common/sp-identifier-reply.png)

    a. In the **Sign On URL** textbox, type the URL used by your users to sign-on to your Ceridian Dayforce HCM application.

    | Environment | URL |
    | :-- | :-- |
    | For production | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | For test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. In the **Identifier** textbox, type the URL using the following pattern:

    | Environment | URL |
    | :-- | :-- |
    | For production | `https://ncpingfederate.dayforcehcm.com/sp` |
    | For test | `https://fs-test.dayforcehcm.com/sp` |

    c. In the **Reply URL** textbox, type the URL used by Azure AD to post the response.

    | Environment | URL |
    | :-- | :-- |
    | For production | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | For test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign-On URL, Identifier and Reply URL. Contact [Ceridian Dayforce HCM Client support team](https://www.ceridian.com/support) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

5. Ceridian Dayforce HCM application expects the SAML assertions in a specific format. Configure the following claims for this application. You can manage the values of these attributes from the **User Attributes** section on application integration page. On the **Set up Single Sign-On with SAML** page, click **Edit** button to open **User Attributes** dialog.

    ![Screenshot shows User Attributes with the Edit icon selected.](common/edit-attribute.png)

6. In the **User Claims** section on the **User Attributes** dialog, configure SAML token attribute as shown in the image above and perform the following steps:

    | Name | Source Attribute|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Click **Add new claim** to open the **Manage user claims** dialog.

    ![Screenshot shows User claims with the option to Add new claim.](common/new-save-attribute.png)

    ![Screenshot shows the Manage user claims dialog box where you can enter the values described.](common/new-attribute-details.png)

    b. In the **Name** textbox, type the attribute name shown for that row.

    c. Leave the **Namespace** blank.

    d. Select Source as **Attribute**.

    e. From the **Source attribute** list, select the user attribute you want to use for your implementation. For example, if you want to use the EmployeeID as unique user identifier and you have stored the attribute value in the ExtensionAttribute2, then select user.extensionattribute2.

    f. Click **Ok**

    g. Click **Save**.

7. On the **Set up Single Sign-On with SAML** page, in the **SAML Signing Certificate** section, click **Download** to download the **Metadata XML** from the given options as per your requirement and save it on your computer.

    ![The Certificate download link](common/metadataxml.png)

8. On the **Set up Ceridian Dayforce HCM** section, copy the appropriate URL(s) as per your requirement.

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

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to Ceridian Dayforce HCM.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **Ceridian Dayforce HCM**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you are expecting a role to be assigned to the users, you can select it from the **Select a role** dropdown. If no role has been set up for this app, you see "Default Access" role selected.
1. In the **Add Assignment** dialog, click the **Assign** button.

### Configure Ceridian Dayforce HCM SSO

To configure single sign-on on **Ceridian Dayforce HCM** side, you need to send the downloaded **Metadata XML** and appropriate copied URLs from Azure portal to [Ceridian Dayforce HCM support team](https://www.ceridian.com/support). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Ceridian Dayforce HCM test user

In this section, you create a user called Britta Simon in Ceridian Dayforce HCM. Work with [Ceridian Dayforce HCM support team](https://www.ceridian.com/support) to add the users in the Ceridian Dayforce HCM platform. Users must be created and activated before you use single sign-on.

### Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application** in Azure portal. This will redirect to Ceridian Dayforce HCM Sign-on URL where you can initiate the login flow. 

* Go to Ceridian Dayforce HCM Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Ceridian Dayforce HCM tile in the My Apps, this will redirect to Ceridian Dayforce HCM Sign-on URL. For more information about the My Apps, see [Introduction to the My Apps](../user-help/my-apps-portal-end-user-access.md).

## Next steps

Once you configure Ceridian Dayforce HCM you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).