---
title: Web app that calls web APIs (calling a web API) - Microsoft identity platform
description: Learn how to build a Web app that calls web APIs (calling a web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev 
#Customer intent: As an application developer, I want to know how to write a Web app that calls web APIs using the Microsoft identity platform for developers.
ms.collection: M365-identity-device-management
---

# Web app that calls web APIs - call a web API

Now that you have a token, you can call a protected web API.

## ASP.NET Core

Here is a simplified code of the action of the `HomeController`. This code gets a token to call the Microsoft Graph. This time code was added, showing how to call Microsoft Graph as a REST API.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> You can use the same principle to call any web API.
>
> Most Azure web APIs provide an SDK that simplifies calling it. This is also the case of the Microsoft Graph. You'll learn in the next article where to find a tutorial illustrating these aspects.

## Next steps

> [!div class="nextstepaction"]
> [Move to production](scenario-web-app-call-api-production.md)
