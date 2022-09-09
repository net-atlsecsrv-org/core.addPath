---
title: Add Microsoft identity platform sign-in to an ASP.NET web app | Azure
description: Learn how to implement Microsoft sign-in on an ASP.NET web app using OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG

ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
#Customer intent: As an application developer, I want to know how to write an ASP.NET web app that can sign in personal accounts, as well as work and school accounts from any Azure Active Directory instance.
---

# Quickstart: Add Microsoft identity platform sign-in to an ASP.NET web app
In this quickstart, you use a code sample to learn how an ASP.NET web app to sign in personal accounts (hotmail.com, outlook.com, others) and work and school accounts from any Azure Active Directory (Azure AD) instance.  (See [How the sample works](#how-the-sample-works) for an illustration.)
> [!div renderon="docs"]
> ## Register and download your quickstart app
> You have two options to start your quickstart application:
> * [Express] [Option 1: Register and auto configure your app and then download your code sample](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Option 2: Register and manually configure your application and code sample](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### Option 1: Register and auto configure your app and then download your code sample
>
> 1. Go to the new  [Azure portal - App registrations](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) pane.
> 1. Enter a name for your application and click **Register**.
> 1. Follow the instructions to download and automatically configure your new application for you in one click.
>
> ### Option 2: Register and manually configure your application and code sample
>
> #### Step 1: Register your application
> To register your application and add the app's registration information to your solution manually, follow these steps:
>
> 1. Sign in to the [Azure portal](https://portal.azure.com) using either a work or school account, or a personal Microsoft account.
> 1. If your account gives you access to more than one tenant, select your account in the top right corner, and set your portal session to the desired Azure AD tenant.
> 1. Navigate to the Microsoft identity platform for developers [App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page.
> 1. Select **New registration**.
> 1. When the **Register an application** page appears, enter your application's registration information:
>      - In the **Name** section, enter a meaningful application name that will be displayed to users of the app, for example `ASPNET-Quickstart`.
>      - Add `http://localhost:44368/` in **Redirect URI**, and click **Register**.
>      - From the left navigation pane under the Manage section, select **Authentication**
>          - Under the **Implicit Grant** sub-section, select **ID tokens**.
>          - And then select **Save**.

> [!div class="sxs-lookup" renderon="portal"]
> #### Step 1: Configure your application in Azure portal
> For the code sample for this quickstart to work, you need to add a reply URL as `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make this change for me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-aspnet-webapp/green-check.png) Your application is configured with this attribute

#### Step 2: Download your project

> [!div renderon="docs"]
> [Download the Visual Studio 2019 solution](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal"]
> Run the project using Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Download the code sample](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### Step 3: Your app is configured and ready to run
> We have configured your project with values of your app's properties. 

> [!div renderon="docs"]
> #### Step 3: Run your Visual Studio project

1. Extract the zip file to a local folder closer to the root folder - for example, **C:\Azure-Samples**
1. Open the solution in Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. Depending on the version of Visual Studio, you might need to right click on the project `AppModelv2-WebApp-OpenIDConnect-DotNet` and **Restore NuGet packages**
1. Open the Package Manager Console (View -> Other Windows -> Package Manager Console) and run `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. Edit **Web.config** and replace the parameters `ClientId` and `Tenant` with:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    Where:
> - `Enter_the_Application_Id_here` - is the Application Id for the application you registered.
> - `Enter_the_Tenant_Info_Here` - is one of the options below:
>   - If your application supports **My organization only**, replace this value with the **Tenant Id** or **Tenant name** (for example, contoso.onmicrosoft.com)
>   - If your application supports **Accounts in any organizational directory**, replace this value with `organizations`
>   - If your application supports **All Microsoft account users**, replace this value with `common`
>
> > [!TIP]
> > - To find the values of *Application ID*, *Directory (tenant) ID*, and *Supported account types*, go to the **Overview** page
> > - Ensure the value for `redirectUri` in the **Web.config** corresponds with the **Redirect URI** defined for the App Registration in Azure AD (if not, navigate to the **Authentication** menu for the App Registration and update the **REDIRECT URI** to match)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

## More information

This section gives an overview of the code required to sign-in users. This overview can be useful to understand how the code works, main arguments, and also if you want to add sign-in to an existing ASP.NET application.

### How the sample works
![Shows how the sample app generated by this quickstart works](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### OWIN middleware NuGet packages

You can set up the authentication pipeline with cookie-based authentication using OpenID Connect in ASP.NET with OWIN Middleware packages. You can install these packages by running the following commands in Visual Studio's **Package Manager Console**:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### OWIN Startup Class

The OWIN middleware uses a *startup class* that runs when the hosting process initializes. In this quickstart, the *startup.cs* file located in root folder. The following code shows the parameter used by this quickstart:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  |  |
> |---------|---------|
> | `ClientId`     | Application ID from the application registered in the Azure portal |
> | `Authority`    | The STS endpoint for user to authenticate. Usually <https://login.microsoftonline.com/{tenant}/v2.0> for public cloud, where {tenant} is the name of your tenant, your tenant Id, or *common* for a reference to the common endpoint (used for multi-tenant applications) |
> | `RedirectUri`  | URL where users are sent after authentication against Microsoft identity platform endpoint |
> | `PostLogoutRedirectUri`     | URL where users are sent after signing-off |
> | `Scope`     | The list of scopes being requested, separated by spaces |
> | `ResponseType`     | Request that the response from authentication contains an ID token |
> | `TokenValidationParameters`     | A list of parameters for token validation. In this case, `ValidateIssuer` is set to `false` to indicate that it can accept sign-ins from any personal, or work or school account types |
> | `Notifications`     | A list of delegates that can be executed on different *OpenIdConnect* messages |


> [!NOTE]
> Setting `ValidateIssuer = false` is a simplification for this quickstart. In real applications you need to validate the issuer.
> See the samples to understand how to do that.

### Initiate an authentication challenge

You can force a user to sign in by requesting an authentication challenge in your controller:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Requesting an authentication challenge using the method above is optional and normally used when you want a view to be accessible from both authenticated and non-authenticated users. Alternatively, you can protect controllers by using the method described in the next section.

### Protect a controller or a controller's method

You can protect a controller or controller actions using the `[Authorize]` attribute. This attribute restricts access to the controller or actions by allowing only authenticated users to access the actions in the controller, which means that authentication challenge will happen automatically when a *non-authenticated* user tries to access one of the actions or controller decorated by the `[Authorize]` attribute.

## Next steps

Try out the ASP.NET tutorial for a complete step-by-step guide on building applications and new features, including a full explanation of this quickstart.

### Learn the steps to create the application used in this quickstart

> [!div class="nextstepaction"]
> [Sign-in tutorial](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help us improve the Microsoft identity platform. Tell us what you think by completing a short two-question survey.

> [!div class="nextstepaction"]
> [Microsoft identity platform survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
