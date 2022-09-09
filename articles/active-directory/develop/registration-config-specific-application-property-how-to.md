---
title: How to fill out specific fields for a custom-developed application | Microsoft Docs
description: Guidance on how to fill out specific fields when you are registering a custom developed application with Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG

ms.assetid: 
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi

ms.collection: M365-identity-device-management
---

# How to fill out specific fields for a custom-developed application

This article gives you a brief description of all the available fields in the application registration form in the [Azure portal](https://portal.azure.com).

## Register a new application

-   To register a new application, navigate to the [Azure portal](https://portal.azure.com).

-   From the left navigation pane, click **Azure Active Directory.**

-   Choose **App registrations** and click **Add**.

-   This open up the application registration form.

## Fields in the application registration form


| Field            | Description                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Name             | The name of the application. It should have a minimum of four characters.                |
| Supported account types| Select which accounts you would like your application to support: accounts in this organizational directory only, accounts in any organizational directory, or accounts in any organizational directory and personal Microsoft accounts.  |
| Redirect URI (optional) | Select the type of app you're building, **Web** or **Public client (mobile & desktop)**, and then enter the redirect URI (or reply URL) for your application. For web applications, provide the base URL of your app. For example, http://localhost:31544 might be the URL for a web app running on your local machine. Users would use this URL to sign in to a web client application. For public client applications, provide the URI used by Azure AD to return token responses. Enter a value specific to your application, such as myapp://auth. To see specific examples for web applications or native applications, check out our [quickstarts](https://docs.microsoft.com/azure/active-directory/develop).|

Once you have filled the above fields, the application is registered in the Azure portal, and you are redirected to the application overview page. The settings pages in the left pane under **Manage** have more fields for you to customize your application. The tables below describe all the fields. You would only see a subset of these fields, depending on whether you created a web application or a public client application.

### Overview
| Field           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Application ID  | When you register an application, Azure AD assigns your application an Application ID. The application ID can be used to uniquely identify your application in authentication requests to Azure AD, as well as to access resources like the Graph API.                                                          |
| App ID URI      | This should be a unique URI, usually of the form **https://&lt;tenant\_name&gt;/&lt;application\_name&gt;.** This is used during the authorization grant flow, as a unique identifier to specify the resource that the token should be issued for. It also becomes the 'aud' claim in the issued access token. |

### Branding

| Field           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Upload new logo | You can use this to upload a logo for your application. The logo must be in .bmp, .jpg or .png format, and the file size should be less than 100 KB. The dimensions for the image should be 215x215 pixels, with central image dimensions of 94x94 pixels.|
| Home page URL   | This is the sign-on URL specified during application registration.|

### Authentication

| Field           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Logout URL      | This is the single sign-out logout URL. Azure AD sends a logout request to this URL when the user clears their session with Azure AD using any other registered application.|
| Supported account types  | This switch specifies whether the application can be used by multiple tenants. Typically, this means that external organizations can use your application by registering it in their tenant and granting access to their organization's data.|
| Redirect URLs      | The redirect, or reply, URLs are the endpoints where Azure AD returns any tokens that your application requests. For native applications, this is where the user is sent after successful authorization. Azure AD checks that the redirect URI your application supplies in the OAuth 2.0 request matches one of the registered values in the portal.|

### Certificates and secrets

| Field           | Description        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Client secrets            | You can create client secrets, or keys, to programmatically access web APIs secured by Azure AD without any user interaction. From the **New client secret** page, enter a key description and the expiration date and save to generate the key. Make sure to save it somewhere secure, as you won't be able to access it later.             |

## Next steps
[Managing Applications with Azure Active Directory](../manage-apps/what-is-application-management.md)
