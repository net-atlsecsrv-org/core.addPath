---
title: How to configure federated single sign-on for an Azure AD Gallery application | Microsoft Docs
description: How to configure federated single sign-on for an existing Azure AD Gallery application, and how to use tutorials to get going quickly
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG

ms.assetid: 
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart

ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
---

# How to configure federated single sign-on for an Azure AD Gallery application

There's a step-by-step tutorial available for all applications in the Azure Active Directory (Azure AD) gallery that have Enterprise single sign-on capability. You can access the [List of tutorials on how to integrate SaaS apps with Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) for detailed step-by-step guidance.

## Overview of steps required
To configure an application from the Azure AD gallery you need to:

-   [Add an application from the Azure AD gallery](#add-an-application-from-the-azure-ad-gallery)

-   [Configure the application’s metadata values in Azure AD (Sign-on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Select User Identifier and add user attributes to be sent to the application](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Retrieve the Azure AD metadata and certificate](#download-the-azure-ad-metadata-or-certificate)

-   [Configure Azure AD metadata values in the application (Sign-on URL, Issuer, Logout URL, and certificate)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Assign users to the application](#assign-users-to-the-application)

## Add an application from the Azure AD gallery

To add an application from the Azure AD Gallery, follow these steps:

1.  Open the [Azure portal](https://portal.azure.com) and sign in as a **Global Administrator** or **Co-admin**.

2.  Open the **Azure Active Directory Extension** by selecting **All services** at the top of the main left-side navigation menu.

3.  Type “Azure Active Directory” in the search box and select **Azure Active Directory**.

4.  Select **Enterprise Applications** from the Azure AD left-side navigation menu.

5.  Select **Add** in the upper-right corner on the **Enterprise Applications** pane.

6.  In the **Enter a name** box in the **Add from the gallery** section, type the name of the application.

7.  Select the application you want to configure for single sign-on.

8.  Before adding the application, you can change its name in the **Name** box.

9.  Select **Add** to add the application.

After a short period, you should be able to see the application’s configuration pane.

## Configure single sign-on for an application from the Azure AD gallery

To configure single sign-on for an application, follow these steps:

1. Open the [Azure portal](https://portal.azure.com/) and sign in as a **Global Administrator** or **Co-admin**.

2. Open the **Azure Active Directory Extension** by selecting **All services** at the top of the main left-side navigation menu.

3. Type “Azure Active Directory” in the search box and select **Azure Active Directory**.

4. Select **Enterprise Applications** from the Azure Active Directory left-side navigation menu.

5. Select **All Applications** to view a list of all your applications.

   * If you don't see the application you want here, use the **Filter** control at the top of the **All Applications List** and set the **Show** option to **All Applications.**

6. Select the application you want to configure single sign-on.

7. After the application loads, select the **Single sign-on** from the application’s left-side navigation menu.

8. Select **SAML-based Sign-on** from the **Mode** dropdown.

9. Enter the required values in **Domain and URLs.** You should get these values from the application vendor.

   1. To configure the application as SP-initiated SSO, the Sign-on URL it’s a required value. For some applications, the Identifier is also a required value.

   2. To configure the application as IdP-initiated SSO, the Reply URL it’s a required value. For some applications, the Identifier is also a required value.

10. **Optional**: Select **Show advanced URL settings** if you want to see the non-required values.

11. In the **User attributes**, select the unique identifier for your users in the **User Identifier** dropdown.

12. **Optional**: Select **View and edit all other user attributes** to edit the attributes to be sent to the application in the SAML token when users sign in.

    To add an attribute:
   
    1. Select **Add attribute**. Enter the **Name** and the select the **Value** from the dropdown.

    1. Select **Save.** You see the new attribute in the table.

13. Select **Configure &lt;application name&gt;** to access documentation on how to configure single sign-on in the application. Also, you have the necessary metadata URLs and certificate to set up SSO with the application.

14. Select **Save** to save the configuration.

15. Assign users to the application.

## Select User Identifier and add user attributes to be sent to the application

To select the User Identifier or add user attributes, follow these steps:

1. Open the [Azure portal](https://portal.azure.com/) and sign in as a **Global Administrator** or **Co-admin.**

2. Open the **Azure Active Directory Extension** by selecting **All services** at the top of the main left-side navigation menu.

3. Type “Azure Active Directory” in the search box and select **Azure Active Directory**.

4. Select **Enterprise Applications** from the Azure Active Directory left-side navigation menu.

5. Select **All Applications** to view a list of all your applications.

   * If you don't see the application you want here, use the **Filter** control at the top of the **All Applications List** and set the **Show** option to **All Applications.**

6. Select the application you have configured with single sign-on.

7. After the application loads, select the **Single sign-on** from the application’s left-side navigation menu.

8. Under the **User attributes** section, select the unique identifier for your users in the **User Identifier** dropdown. The selected option needs to match the expected value in the application to authenticate the user.

   >[!NOTE] 
   >Azure AD selects the format for the NameID attribute (user Identifier) based on the value selected or the format requested by the application in the SAML AuthRequest. For more information, see [Single Sign-On SAML protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) in the NameIDPolicy section.
   >
   >

9. To add user attributes, select **View and edit all other user attributes** to edit the attributes to be sent to the application in the SAML token when users sign in.

   To add an attribute:
  
   1. Select **Add attribute**. Enter the **Name** and the select the **Value** from the dropdown.

   2. Select **Save**. You see the new attribute in the table.

## Download the Azure AD metadata or certificate

To download the application metadata or certificate from Azure AD, follow these steps:

1. Open the [Azure portal](https://portal.azure.com/) and sign in as a **Global Administrator** or **Co-admin.**

2. Open the **Azure Active Directory Extension** by selecting **All services** at the top of the main left-side navigation menu.

3. Type “Azure Active Directory” in the search box and select **Azure Active Directory**.

4. Select **Enterprise Applications** from the Azure Active Directory left-side navigation menu.

5. Select **All Applications** to view a list of all your applications.

   *  If you don't see the application you want here, use the **Filter** control at the top of the **All Applications List** and set the **Show** option to **All Applications**.

6. Select the application you have configured with single sign-on.

7. After the application loads, select the **Single sign-on** from the application’s left-side navigation menu.

8. Go to **SAML Signing Certificate** section, then select **Download** column value. Depending on what the application requires configuring single sign-on, you see either the option to download the Metadata XML or the Certificate.

Azure AD also provides a URL to access the metadata. Use the following model to get the metadata URL specific to the application: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## Assign users to the application

To assign one or more users to an application directly, follow these steps:

1. Open the [Azure portal](https://portal.azure.com/) and sign in as a **Global Administrator.**

2. Open the **Azure Active Directory Extension** by selecting **All services** at the top of the main left-side navigation menu.

3. Type “Azure Active Directory” in the search box and select **Azure Active Directory**.

4. Select **Enterprise Applications** from the Azure Active Directory left-side navigation menu.

5. Select **All Applications** to view a list of all your applications.

   * If you don't see the application you want here, use the **Filter** control at the top of the **All Applications List** and set the **Show** option to **All Applications.**

6. Select the application you want to assign a user to from the list.

7. After the application loads, select **Users and Groups** from the application’s left-side navigation menu.

8. Select the **Add** button on top of the **Users and Groups** list to open the **Add Assignment** pane.

9. Select the **Users and groups** selector from the **Add Assignment** pane.

10. Type the **full name** or **email address** of the user you want to assign into the **Search by name or email address** search box.

11. Hover over the **user** in the list to reveal a **check box**. Select the check box next to the user’s profile photo or logo to add your user to the **Selected** list.

12. **Optional**: If you want to **add more than one user**, type another **full name** or **email address** into the **Search by name or email address** search box, and select the check box to add this user to the **Selected** list.

13. When you're finished selecting users, select the **Select** button to add them to the list of users and groups to be assigned to the application.

14. **Optional**: Select the **Select Role** selector in the **Add Assignment** pane to select a role to assign to the users you have selected.

15. Select the **Assign** button to assign the application to the selected users.

After a short period of time, the users you have selected be able to launch these applications using the methods described in the solution description section.

## Customizing the SAML claims sent to an application

To learn how to customize the SAML attribute claims sent to your application, see [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## Next steps
[Provide single sign-on to your apps with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)



