---
title: "Quickstart: Protect an ASP.NET Core web API with the Microsoft identity platform | Azure"
titleSuffix: Microsoft identity platform
description: In this quickstart, you download and modify a code sample that demonstrates how to protect an ASP.NET Core web API by using the Microsoft identity platform for authorization.
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: "devx-track-csharp, scenarios:getting-started, languages:aspnet-core"
# Customer intent: As an application developer, I want to know how to write an ASP.NET Core web API that uses the Microsoft identity platform to authorize API requests from clients.
---

# Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform

In this quickstart, you use a code sample to learn how to protect an ASP.NET Core web API so than it can be accessed only by authorized accounts. Accounts can be personal accounts (hotmail.com, outlook.com, and others) and work and school accounts in any Azure Active Directory (Azure AD) instance.

> [!div renderon="docs"]
> ## Prerequisites
>
> - An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory tenant](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) or [Visual Studio Code](https://code.visualstudio.com/)
>
> ## Step 1: Register the application
>
> First, register the web API in your Azure AD tenant and add a scope by following these steps:
>
> 1. Sign in to the [Azure portal](https://portal.azure.com).
> 1. If you have access to multiple tenants, use the **Directory + subscription** filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in the top menu to select the tenant in which you want to register an application.
> 1. Search for and select **Azure Active Directory**.
> 1. Under **Manage**, select **App registrations**, then **New registration**.
> 1. Enter a **Name** for your application, for example `AspNetCoreWebApi-Quickstart`. Users of your app might see this name, and you can change it later.
> 1. Select **Register**.
> 1. Under **Manage**, select **Expose an API**
> 1. Select **Add a scope** and select **Save and continue** to accept the default **Application ID URI**.
> 1. In the **Add a scope** pane, enter the following values:
>    - **Scope name**: `access_as_user`
>    - **Who can consent?**: **Admins and users**
>    - **Admin consent display name**: `Access AspNetCoreWebApi-Quickstart`
>    - **Admin consent description**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **User consent display name**: `Access AspNetCoreWebApi-Quickstart`
>    - **User consent description**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **State**: **Enabled**
> 1. Select **Add scope** to complete the scope addition.

## Step 2: Download the ASP.NET Core project

> [!div renderon="docs"]
> [Download the ASP.NET Core solution](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) from GitHub.

> [!div renderon="docs"]
> ## Step 3: Configure the ASP.NET Core project
>
> In this step, configure the sample code to work with the app registration you created earlier.
>
> 1. Extract the .zip archive into a folder near the root of your drive. For example, into *C:\Azure-Samples*.
> 1. Open the solution in the *webapi* folder in your code editor.
> 1. Open the *appsettings.json* file and modify the following:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Replace `Enter_the_Application_Id_here` with the **Application (client) ID** of the application you registered in the Azure portal. You can find **Application (client) ID** in the app's **Overview** page.
>    - Replace `Enter_the_Tenant_Info_Here` with one of the following:
>       - If your application supports **Accounts in this organizational directory only**, replace this value with the **Directory (tenant) ID** (a GUID) or **tenant name** (for example, `contoso.onmicrosoft.com`). You can find the **Directory (tenant) ID** on the app's **Overview** page.
>       - If your application supports **Accounts in any organizational directory**, replace this value with `organizations`
>       - If your application supports **All Microsoft account users**, leave this value as `common`
>
> For this quickstart, don't change any other values in the *appsettings.json* file.

## How the sample works

The web API receives a token from a client application, and the code in the web API validates the token. This scenario is explained in more detail in [Scenario: Protected web API](scenario-protected-web-api-overview.md).

### Startup class

The *Microsoft.AspNetCore.Authentication* middleware uses a `Startup` class that's executed when the hosting process initializes. In its `ConfigureServices` method, the `AddMicrosoftIdentityWebApi` extension method provided by *Microsoft.Identity.Web* is called.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

The `AddAuthentication()` method configures the service to add JwtBearer-based authentication.

The line containing `.AddMicrosoftIdentityWebApi` adds Microsoft identity platform authorization to your web API. It's then configured to validate access tokens issued by the Microsoft identity platform endpoint based on the information in the `AzureAD` section of the *appsettings.json* configuration file:

| *appsettings.json* key | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Application (client) ID** of the application registered in the Azure portal.                                                                                       |
| `Instance`             | Security token service (STS) endpoint for the user to authenticate. This value is typically `https://login.microsoftonline.com/`, indicating the Azure public cloud. |
| `TenantId`             | Name of your tenant or its tenant ID (a GUID), or *common* to sign in users with work or school accounts or Microsoft personal accounts.                             |

The `Configure()` method contains two important methods, `app.UseAuthentication()` and `app.UseAuthorization()`, that enable their named functionality:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### Protect a controller, a controller's method, or a Razor page

You can protect a controller or controller methods using the `[Authorize]` attribute. This attribute restricts access to the controller or methods by only allowing authenticated users, which means that authentication challenge can be started to access the controller if the user isn't authenticated.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### Validate the scope in the controller

The code in the API then verifies that the required scopes are in the token by using `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## Next steps

The GitHub repository that contains this ASP.NET Core web API code sample includes instructions and more code samples that show you how to:

- Add authentication to a new ASP.NET Core web API
- Call the web API from a desktop application
- Call downstream APIs like Microsoft Graph and other Microsoft APIs

> [!div class="nextstepaction"]
> [ASP.NET Core web API tutorials on GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
