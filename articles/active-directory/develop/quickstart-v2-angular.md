---
title: Sign in users in Angular single-page apps - Azure
titleSuffix: Microsoft identity platform
description: Learn how an Angular app can call an API that requires access tokens using the Microsoft identity platform.
services: active-directory
author: jasonnutter
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter

#Customer intent: As an app developer, I want to learn how to get access tokens by using the Microsoft identity platform endpoint so that my Angular app can sign in users of personal Microsoft accounts, and work and school accounts.
---

# Quickstart: Sign in users and get an access token in an Angular SPA

> [!IMPORTANT]
> This feature is currently in preview. Previews are made available to you on the condition that you agree to the [supplemental terms of use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Some aspects of this feature may change prior to general availability (GA).

In this quickstart, you use a code sample to learn how an Angular single-page application (SPA) can sign in users who have personal Microsoft accounts, and work accounts and school accounts. An Angular SPA can also get an access token to call the Microsoft Graph API or any web API.

## Prerequisites

* Azure subscription - [create one for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) to edit project files, or [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) to run the project.

> [!div renderon="docs"]
> ## Register and download the quickstart app
> To start the quickstart app, use either of the following options:
>
> ### Option 1 (Express): Register and auto configure the app. Then download the code sample
>
> 1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
> 1. If your account has access to more than one tenant, select the account at the top right, and then set your portal session to the Azure Active Directory (Azure AD) tenant you want to use.
> 1. Open the new [Azure portal - App registrations](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) pane.
> 1. Enter a name for your application, and then select **Register**.
> 1. Navigate to the quickstart pane and view the Angular quickstart. Follow the instructions to download and automatically configure your new application.
>
> ### Option 2 (Manual): Register and manually configure the application and code sample
>
> #### Step 1: Register the application
>
> 1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
>
> 1. If your account has access to more than one tenant, select your account at the top right, and set your portal session to the Azure AD tenant you want to use.
> 1. Follow the instructions to [register a single-page application](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) in the Azure portal.
> 1. Add a new platform in the **Authentication blade** of your app registration and register the redirect URI: `http://localhost:4200/`
> 1. This quickstart uses the [implicit grant flow](v2-oauth2-implicit-grant-flow.md). Select the **Implicit Grant** settings for **ID tokens** and **Access Tokens**. ID tokens and access tokens are required because this app signs in users and calls an API.

> [!div class="sxs-lookup" renderon="portal"]
> #### Step 1: Configure the application in the Azure portal
> For the code sample for this quickstart to work, you need to add a `redirectUri` as `http://localhost:4200/` and enable **Implicit grant**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-javascript/green-check.png) Your application is configured with these attributes.

#### Step 2: Download the code sample
>[!div renderon="docs"]
>To run the project with a web server by using Node.js, clone https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular or [download](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) the core project files. Open the files using an editor such as Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Download the code sample](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### Step 3: Configure the JavaScript app
>
> In the *src/app* folder, edit *app.module.ts* and set the `clientId` and `authority` values under `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Replace these values as such:
>
>|Value name|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|In the **overview** page of your application registration, this is your **application(client) ID** |
>|Enter_the_Cloud_Instance_Id_Here|This is the instance of the Azure cloud. For the main or global Azure cloud, enter https://login.microsoftonline.com. For national clouds (for example, China), see [National clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Set to one of the following options: 1) If your application supports *accounts in this organizational directory*, replace this value with the **Directory (Tenant) ID** or **Tenant name** (for example, *contoso.microsoft.com*). 2) If your application supports *accounts in any organizational directory*, replace this value with **organizations**. 3) If your application supports *accounts in any organizational directory and personal Microsoft accounts*, replace this value with **common**. 4) To restrict support to *personal Microsoft accounts only*, replace this value with **consumers**. |
>|Enter_the_Redirect_Uri_Here|Replace with `http://localhost:4200`|
>|cacheLocation  | (Optional) Sets the browser storage for the auth state. The default is sessionStorage.   |
>|storeAuthStateInCookie  | (Optional) The library that stores the authentication request state. This state is required to validate the authentication flows in the browser cookies. This cookie is set for the Internet Explorer and Edge browsers to accommodate those two browsers. For more details, see [known issues](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > To find the values of **Application (client) ID**, **Directory (tenant) ID**, and **Supported account types**, go to the app's **Overview** page in the Azure portal.

For more information about available configurable options, see [Initialize client applications](msal-js-initializing-client-applications.md).

>[!div class="sxs-lookup" renderon="portal"]
>#### Step 3: Run the project

>[!div renderon="docs"]
>#### Step 4: Run the project

* If you're using Node.js:

    1. Start the server by running the following commands from the project directory:

        ```console
        npm install
        npm start
        ```

    1. Browse `http://localhost:4200/`.
    1. Select **Login**.
    1. Select **Profile** to call Microsoft Graph.

After the browser loads the application, select **Login**. The first time you sign in, you're prompted to provide your consent to allow the application to access your profile and sign you in. After you're signed in successfully, select **Profile**, and your user profile information will be displayed on the page.

## How the sample works

![How the sample app in this quickstart works](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## Next steps

> [!div class="nextstepaction"]
> Go to the [Angular Tutorial](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular) to learn how to sign in a user and acquire tokens.

Browse the [MSAL repo](https://github.com/AzureAD/microsoft-authentication-library-for-js) for documentation, FAQ, issues, and more.
