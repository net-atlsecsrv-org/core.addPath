---
title: Register a web app that signs in users - Microsoft identity platform | Azure
description: Learn how to register a web app that signs in users
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
#Customer intent: As an application developer, I want to know how to write a web app that signs in users by using the Microsoft identity platform for developers.
---

# Web app that signs in users: App registration

This article explains the app registration specifics for a web app that signs in users.

To register your application, you can use:

- The [web app quickstarts](#register-an-app-by-using-the-quickstarts). In addition to being a great first experience with creating an application, quickstarts in the Azure portal contain a button named **Make this change for me**. You can use this button to set the properties you need, even for an existing app. You'll need to adapt the values of these properties to your own case. In particular, the web API URL for your app is probably going to be different from the proposed default, which will also affect the sign-out URI.
- The Azure portal to [register your application manually](#register-an-app-by-using-the-azure-portal).
- PowerShell and command-line tools.

## Register an app by using the quickstarts

You can use these links to bootstrap the creation of your web application:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## Register an app by using the Azure portal

> [!NOTE]
> The portal to use is different depending on whether your application runs in the Microsoft Azure public cloud or in a national or sovereign cloud. For more information, see [National clouds](./authentication-national-cloud.md#app-registration-endpoints).


1. Sign in to the [Azure portal](https://portal.azure.com). 
1. If you have access to multiple tenants, use the **Directory + subscription** filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in the top menu to select the tenant in which you want to register an application.
1. Search for and select **Azure Active Directory**.
1. Under **Manage**, select **App registrations** > **New registration**.

# [ASP.NET Core](#tab/aspnetcore)

1. When the **Register an application** page appears, enter your application's registration information:
   1. Enter a **Name** for your application, for example `AspNetCore-WebApp`. Users of your app might see this name, and you can change it later.
   1. Choose the supported account types for your application. (See [Supported account types](./v2-supported-account-types.md).)
   1. For **Redirect URI**, add the type of application and the URI destination that will accept returned token responses after successful authentication. For example, enter `https://localhost:44321`.
   1. Select **Register**.
1. Under **Manage**, select **Authentication** and then add the following information:
   1. In the **Web** section, add `https://localhost:44321/signin-oidc` as a **Redirect URI**.
   1. Add `https://localhost:44321/signout-oidc` as a **Logout URL**.
   1. Under **Implicit grant**, select **ID tokens**.
   1. Select **Save**.
   
# [ASP.NET](#tab/aspnet)

1. When the **Register an application page** appears, enter your application's registration information:
   1. Enter a **Name** for your application, for example `MailApp-openidconnect-v2`. Users of your app might see this name, and you can change it later.
   1. Choose the supported account types for your application. (See [Supported account types](./v2-supported-account-types.md).)
   1. In the **Redirect URI (optional)** section, select **Web** in the combo box and enter the following redirect URI: **https://localhost:44326/**.
   1. Select **Register** to create the application.
1. Under **Manage**, select **Authentication**.
1. In the **Implicit grant** section, select **ID tokens**. This sample requires the [implicit grant flow](v2-oauth2-implicit-grant-flow.md) to be enabled to sign in the user.
1. Select **Save**.

# [Java](#tab/java)

1. When the **Register an application page** appears, enter your application's registration information: 
    1. Enter a **Name** for your application, for example `java-webapp`. Users of your app might see this name, and you can change it later. 
    1. Select **Accounts in any organizational directory and personal Microsoft Accounts (e.g. Skype, Xbox, Outlook.com)**.
    1. Select **Register** to register the application.
1. Under **Manage**, select **Authentication** > **Add a platform**.
1. Select **Web**.
1. For **Redirect URI**, enter the same host and port number, followed by `/msal4jsample/secure/aad` for the sign-in page. 
1. Select **Configure**.
1. In the **Web** section, use the host and port number, followed by **/msal4jsample/graph/me** as a **Redirect URI** for the user information page.
By default, the sample uses:
   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

1. Select **Save**.
1. Under **Manage**, select **Certificates & secrets**.
1. In the **Client secrets** section, select **New client secret**, and then:

   1. Enter a key description.
   1. Select the key duration **In 1 year**.
   1. Select **Add**.
   1. When the key value appears, copy it for later. This value will not be displayed again or be retrievable by any other means.

# [Python](#tab/python)

1. When the **Register an application page** appears, enter your application's registration information:
   1. Enter a **Name** for your application, for example `python-webapp`. Users of your app might see this name, and you can change it later.
   1. Change **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts (e.g. Skype, Xbox, Outlook.com)**.
   1. In the **Redirect URI (optional)** section, select **Web** in the combo  box and enter the following redirect URI: **http://localhost:5000/getAToken**.
   1. Select **Register** to create the application.
1. On the app's **Overview** page, find the **Application (client) ID** value and record it for later. You'll need it to configure the Visual Studio configuration file for this project.
1. Under **Manage**, select **Certificates & secrets**.
1. In the **Client Secrets** section, select **New client secret**, and then:
   1. Enter a key description.
   1. Select a key duration of **In 1 year**.
   1. Select **Add**.
   1. When the key value appears, copy it. You'll need it later.
---

## Register an app by using PowerShell

> [!NOTE]
> Currently, Azure AD PowerShell creates applications with only the following supported account types:
>
> - MyOrg (accounts in this organizational directory only)
> - AnyOrg (accounts in any organizational directory)
>
> You can create an application that signs in users with their personal Microsoft accounts (for example, Skype, Xbox, or Outlook.com). First, create a multitenant application. Supported account types are accounts in any organizational directory. Then, change the [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) property to **2** and the [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) property to `AzureADandPersonalMicrosoftAccount` in the [application manifest](./reference-app-manifest.md) from the Azure portal. For more information, see [step 1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) in the ASP.NET Core tutorial. You can generalize this step to web apps in any language.

## Next steps

Move on to the next article in this scenario,
[App's code configuration](scenario-web-app-sign-user-app-configuration.md).
