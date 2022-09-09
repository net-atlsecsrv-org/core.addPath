---
title: Get a token for a web API that calls web APIs | Azure
titleSuffix: Microsoft identity platform
description: Learn how to build a web API that calls web APIs that require acquiring a token for the app.
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
#Customer intent: As an application developer, I want to know how to write a web API that calls web APIs by using the Microsoft identity platform for developers.
---

# A web API that calls web APIs: Acquire a token for the app

After you've built a client application object, use it to acquire a token that you can use to call a web API.

## Code in the controller

### [ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* adds extension methods that provide convenience services for calling Microsoft Graph or a downstream web API. These methods are explained in detail in [A web API that calls web APIs: Call an API](scenario-web-api-call-api-call-api.md). With these helper methods, you don't need to manually acquire a token.

If, however, you do want to manually acquire a token, the following code shows an example of using *Microsoft.Identity.Web* to do so in an API controller. It calls a downstream API named *todolist*.
To get a token to call the downstream API, you inject the `ITokenAcquisition` service by dependency injection in your controller's constructor (or your page constructor if you use Blazor), and you use it in your controller actions, getting a token for the user (`GetAccessTokenForUserAsync`) or for the application itself (`GetAccessTokenForAppAsync`) in the case of a daemon scenario.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

For details about the `callTodoListService` method, see  [A web API that calls web APIs: Call an API](scenario-web-api-call-api-call-api.md).

### [Java](#tab/java)

Here's an example of code that's called in the actions of the API controllers. It calls the downstream API - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### [Python](#tab/python)

A Python web API requires the use of middleware to validate the bearer token received from the client. The web API can then obtain the access token for a downstream API using the MSAL Python library by calling the [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) method. A sample demonstrating this flow with MSAL Python isn't yet available.

## (Advanced) Accessing the signed-in user's token cache from background apps, APIs and services

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## Next steps

Move on to the next article in this scenario,
[Call an API](scenario-web-api-call-api-call-api.md).
