---
title: Use MSAL.js with Azure AD B2C
titleSuffix: Microsoft identity platform
description: The Microsoft Authentication Library for JavaScript (MSAL.js) enables applications to work with Azure AD B2C and acquire tokens to call secured web APIs. These web APIs can be Microsoft Graph, other Microsoft APIs, web APIs from others, or your own web API.
services: active-directory
author: negoe
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
# Customer intent: As an application developer, I want to learn how MSAL.js can be used with Azure AD B2C for authentication and authorization in my organization's web apps and web APIs that my customers log in to and use.
---

# Use the Microsoft Authentication Library for JavaScript to work with Azure AD B2C

The [Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) enables JavaScript developers to authenticate users with social and local identities using [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C).

By using Azure AD B2C as an identity management service, you can customize and control how your customers sign up, sign in, and manage their profiles when they use your applications. Azure AD B2C also enables you to brand and customize the UI that your application displays during the authentication process.

## Supported app types and scenarios

MSAL.js enables [single-page applications](https://docs.microsoft.com/azure/active-directory-b2c/application-types#single-page-applications) to sign-in users with Azure AD B2C using the [authorization code flow with PKCE](https://docs.microsoft.com/azure/active-directory-b2c/authorization-code-flow) grant. With MSAL.js and Azure AD B2C:

- Users **can** authenticate with their social and local identities.
- Users **can** be authorized to access Azure AD B2C protected resources (but not Azure AD protected resources).
- Users **cannot** obtain tokens for Microsoft APIs (e.g. MS Graph API) using [delegated permissions](https://review.docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent?branch=master#permission-types).
- Users with administrator privileges **can** obtain tokens for Microsoft APIs (e.g. MS Graph API) using [delegated permissions](https://review.docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent?branch=master#permission-types).

For more information, see: [Working with Azure AD B2C](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/working-with-b2c.md)

## Next steps

Follow the tutorial on how to:

- [Sign in users with Azure AD B2C in a single-page application](../../active-directory-b2c/tutorial-single-page-app.md)
- [Call an Azure AD B2C protected web API](../../active-directory-b2c/tutorial-single-page-app-webapi.md)
