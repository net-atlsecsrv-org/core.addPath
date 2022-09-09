---
title: Web app that signs-in users (move to production) - Microsoft identity platform
description: Learn how to build a web app that signs in users (move to production)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev 
#Customer intent: As an application developer, I want to know how to write a web app that signs-in users using the Microsoft identity platform for developers.
ms.collection: M365-identity-device-management
---

# Web app that signs in users - move to production

Now that you know how to acquire a token to call web APIs, learn how to move it to production.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## Next steps

### Calling web APIs scenario

Once your web app signs-in users, it can call web APIs on behalf of the signed-in users. Calling web APIs from the web app is the object of the following scenario:

> [!div class="nextstepaction"]
> [Web app that calls web APIs](scenario-web-app-call-api-overview.md)

### Deep dive - ASP.NET Core web app tutorial

Learn about other ways of sign-in users with the ASP.NET Core tutorial: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). This sample is a progressive tutorial with production ready code for a web app including how to add sign in with accounts in:

- your organization,
- multiple organizations,
- work or school accounts or personal Microsoft account,
- with [Azure AD B2C](https://aka.ms/aadb2c),
- or in national clouds.

### Sample code - Java web app

Learn more about the Java web app from the sample on GitHub: [A Java Web application that signs in users with the Microsoft identity platform and calls Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
