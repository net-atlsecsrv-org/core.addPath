---
title: Call an ASP.NET web API that is protected by Microsoft identity platform
description: In this quickstart, learn how to call an ASP.NET web API that's protected by Microsoft identity platform from a Windows Desktop (WPF) application.
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: "devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET"
#Customer intent: As an application developer, I want to know how to set up OpenId Connect authentication in a web application that's built by using Node.js with Express.
---

# Quickstart: Call an ASP.NET web API that's protected by Microsoft identity platform

In this quickstart, you expose a web API and protect it so that only authenticated users can access it. The article shows how to expose an ASP.NET web API so it can accept tokens that are issued by personal accounts, such as outlook.com or live.com, and work or school accounts from any company or organization that has integrated with Microsoft identity platform.

The article also uses a Windows Presentation Foundation (WPF) app to demonstrate how you can request an access token to access a web API.

## Prerequisites

To run the sample code in this article, you need:

* Visual Studio 2017 or 2019.  Download [Visual Studio for free](https://www.visualstudio.com/downloads/).
* Either a [Microsoft account](https://www.outlook.com) or the [Microsoft 365 Developer Program](/office/developer-program/office-365-developer-program).

## Clone or download the sample

You can obtain the sample in either of two ways:  

* Clone it from your shell or command line:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```  
* [Download it as a ZIP file](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## Register your web API

In this section, you register your web API in the **App registrations** portal.

### Choose your Azure AD tenant

To register your apps manually, choose the Azure Active Directory (Azure AD) tenant where you want to create your apps.

1. Sign in to the [Azure portal](https://portal.azure.com) with either a work or school account or a personal Microsoft account.

1. If your account is present in more than one Azure AD tenant, select your profile at the upper right, and then select **Switch directory**.
1. Change your portal session to the Azure AD tenant you want to use.

### Register the TodoListService app

1. Go to the Microsoft identity platform for developers [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) portal.
1. Select **New registration**.
1. When the **Register an application page** opens, enter your application's registration information:

   a. In the **Name** section, enter a meaningful application name that will be displayed to app users. For example, enter **AppModelv2-NativeClient-DotNet-TodoListService**.  
   b. For **Supported account types**, select **Accounts in any organizational directory**.  
   c. Select **Register** to create the application.

1. On the app **Overview** page, look for the **Application (client) ID** value, and then record it for later use. You'll need it to configure the Visual Studio configuration file for this project (that is, `ClientId` in the *TodoListService\Web.config* file).
1. In the **Expose an API** section, select **Add a scope**, accept the proposed Application ID URI (api://{clientId}) by selecting **Save and Continue**, and then enter the following information:
 
   a. For **Scope name**, enter **access_as_user**.  
   b. For **Who can consent**, ensure that the **Admins and users** option is selected.  
   c. In the **Admin consent display name** box, enter **Access TodoListService as a user**.  
   d. In the **Admin consent description** box, enter **Accesses the TodoListService web API as a user**.  
   e. In the **User consent display name** box, enter **Access TodoListService as a user**.  
   f. In the **User consent description** box, enter **Accesses the TodoListService web API as a user**.  
   g. For **State**, keep **Enabled**.  
   h. Select **Add scope**.

### Configure the service project

Configure the service project to match the registered web API by doing the following:

1. Open the solution in Visual Studio, and then open the *Web.config* file under the root of the TodoListService project.

1. Replace the value of the `ida:ClientId` parameter with the Client ID (Application ID) value from the application you just registered in the **App registrations** portal.

### Add the new scope to the app.config file

To add the new scope to the TodoListClient *app.config* file, do the following:

1. In the TodoListClient project root folder, open the *app.config* file.

1. Paste the Application ID from the application you just registered for your TodoListService project in the `TodoListServiceScope` parameter, replacing the `{Enter the Application ID of your TodoListService from the app registration portal}` string.

  > [!NOTE]
  > Make sure that the Application ID uses the following format: `api://{TodoListService-Application-ID}/access_as_user` (where `{TodoListService-Application-ID}` is the GUID representing the Application ID for your TodoListService app).

## Register the TodoListClient client app

In this section, you register your TodoListClient app in **App registrations** in the Azure portal, and then configure the code in the TodoListClient project. If the client and server are considered *the same application*, you can reuse the application that's registered in step 2. Use the same application if you want users to sign in with a Microsoft personal account.

### Register the app

To register the TodoListClient app, do the following:

1. Go to the Microsoft identity platform for developers [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) portal.
1. Select **New registration**.
1. When the **Register an application page** opens, enter your application's registration information:

   a. In the **Name** section, enter a meaningful application name that will be displayed to users of the app (for example, **NativeClient-DotNet-TodoListClient**).  
   b. For **Supported account types**, select **Accounts in any organizational directory**.  
   c. Select **Register** to create the application.
   
   > [!NOTE]
   > In the TodoListClient project *app.config* file, the default value of `ida:Tenant` is set to `common`. The possible values are:
   > - `common`: You can sign in by using a work or school account or a Microsoft personal account (because you selected **Accounts in any organizational directory** in step 3b).
   > - `organizations`: You can sign in by using a work or school account.
   > - `consumers`: You can sign in only by using a Microsoft personal account.
   >
   
1. On the app **Overview** page, select **Authentication**, and then do the following:

   a. Under **Platform configurations**, select the **Add a platform** button.  
   b. For **Mobile and desktop applications**, select **Mobile and desktop applications**.  
   c. For **Redirect URIs**, select the **https://login.microsoftonline.com/common/oauth2/nativeclient** check box.  
   d. Select **Configure**.   
1. Select **API permissions**, and then do the following:

   a. Select the **Add a permission** button.  
   b. Select the **My APIs** tab.  
   c. In the list of APIs, select **AppModelv2-NativeClient-DotNet-TodoListService API** or the name you entered for the web API.  
   d. Select the **access_as_user** permission check box if it's not already selected. Use the Search box if necessary.  
   e. Select the **Add permissions** button.

### Configure your project

To configure your TodoListClient project, do the following:

1. In the **App registrations** portal, on the **Overview** page, copy the value of the **Application (client) ID**.

1. From the TodoListClient project root folder, open the *app.config* file, and then paste the Application ID value in the `ida:ClientId` parameter.

## Run your TodoListClient project

To run your TodoListClient project, do the following:

1. Press F5 to open your TodoListClient project. The project page should open.

1. At the upper right, select **Sign in**, and then sign in with the same credentials you used to register your application, or sign in as a user in the same directory.

   If you're signing in for the first time, you might be prompted to consent to the TodoListService web API.

   To help you access the TodoListService web API and manipulate the *To-Do* list, the sign-in also requests an access token to the *access_as_user* scope.

## Pre-authorize your client application

One way you can allow users from other directories to access your web API is to pre-authorize the client application to access your web API. You do this by adding the Application ID from the client app to the list of pre-authorized applications for your web API. By adding a pre-authorized client, you're allowing users to access your web API without having to provide consent. To pre-authorize your client app, do the following:

1. In the **App registrations** portal, open the properties of your TodoListService app.
1. In the **Expose an API** section, under **Authorized client applications**, select **Add a client application**.
1. In the **Client ID** box, paste the Application ID of the TodoListClient app.
1. In the **Authorized scopes** section, select the scope for the `api://<Application ID>/access_as_user` web API.
1. Select **Add application**.

## Run your project

1. Press F5 to run your project. Your TodoListClient app should open.
1. At the upper right, select **Sign in**, and then sign in by using a personal Microsoft account, such as live.com or hotmail.com, or a work or school account.

## Optional: Limit sign-in access to certain users

By default, when you've followed the preceding steps, any personal accounts, such as outlook.com or live.com, or work or school accounts from organizations that are integrated with Azure AD can request tokens and access your web API.

To specify who can sign in to your application, use one of the following options:

### Option 1: Limit access to a single organization (single tenant)

You can limit sign-in access to your application to user accounts that are in a single Azure AD tenant, including *guest accounts* of that tenant. This scenario is common for *line-of-business applications*.

1. Open the *App_Start\Startup.Auth* file, and then change the value of the metadata endpoint that's passed into the `OpenIdConnectSecurityTokenProvider` to `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`. You can also use the tenant name, such as `contoso.onmicrosoft.com`.
2. In the same file, set the `ValidIssuer` property on the `TokenValidationParameters` to `"https://sts.windows.net/{Tenant ID}/"`, and set the `ValidateIssuer` argument to `true`.

### Option 2: Use a custom method to validate issuers

You can implement a custom method to validate issuers by using the `IssuerValidator` parameter. For more information about this parameter, see [TokenValidationParameters class](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## Next steps
Learn more about the protected web API scenario that the Microsoft identity platform supports:
> [!div class="nextstepaction"]
> [Protected web API scenario](scenario-protected-web-api-overview.md)
