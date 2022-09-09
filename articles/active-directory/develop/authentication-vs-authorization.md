---
title: Authentication vs. authorization | Azure
titleSuffix: Microsoft identity platform
description: Learn about the basics of authentication and authorization in the Microsoft identity platform.
services: active-directory
author: rwike77
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
#Customer intent: As an application developer, I want to understand the basic concepts of authentication and authorization in the Microsoft identity platform.
---

# Authentication vs. authorization

This article defines authentication and authorization. It also briefly covers how you can use the Microsoft identity platform to authenticate and authorize users in your web apps, web APIs, or apps that call protected web APIs. If you see a term you aren't familiar with, try our [glossary](developer-glossary.md) or our [Microsoft identity platform videos](identity-videos.md), which cover basic concepts.

## Authentication

*Authentication* is the process of proving that you are who you say you are. It's sometimes shortened to *AuthN*. The Microsoft identity platform uses the [OpenID Connect](https://openid.net/connect/) protocol for handling authentication.

## Authorization

*Authorization* is the act of granting an authenticated party permission to do something. It specifies what data you're allowed to access and what you can do with that data. Authorization is sometimes shortened to *AuthZ*. The Microsoft identity platform uses the [OAuth 2.0](https://oauth.net/2/) protocol for handling authorization.

## Authentication and authorization using the Microsoft identity platform

Creating apps that each maintain their own username and password information incurs a high administrative burden when adding or removing users across multiple apps. Instead, your apps can delegate that responsibility to a centralized identity provider.

Azure Active Directory (Azure AD) is a centralized identity provider in the cloud. Delegating authentication and authorization to it enables scenarios such as:

- Conditional Access policies that require a user to be in a specific location.
- The use of [multi-factor authentication](../authentication/concept-mfa-howitworks.md), which is sometimes called two-factor authentication or 2FA.
- Enabling a user to sign in once and then be automatically signed in to all of the web apps that share the same centralized directory. This capability is called *single sign-on (SSO)*.

The Microsoft identity platform simplifies authorization and authentication for application developers by providing identity as a service. It supports industry-standard protocols and open-source libraries for different platforms to help you start coding quickly. It allows developers to build applications that sign in all Microsoft identities, get tokens to call [Microsoft Graph](https://developer.microsoft.com/graph/), access Microsoft APIs, or access other APIs that developers have built.

This video explains the Microsoft identity platform and the basics of modern authentication: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Here's a comparison of the protocols that the Microsoft identity platform uses:

* **OAuth versus OpenID Connect**: The platform uses OAuth for authorization and OpenID Connect (OIDC) for authentication. OpenID Connect is built on top of OAuth 2.0, so the terminology and flow are similar between the two. You can even both authenticate a user (through OpenID Connect) and get authorization to access a protected resource that the user owns (through OAuth 2.0) in one request. For more information, see [OAuth 2.0 and OpenID Connect protocols](active-directory-v2-protocols.md) and [OpenID Connect protocol](v2-protocols-oidc.md).
* **OAuth versus SAML**: The platform uses OAuth 2.0 for authorization and SAML for authentication. For more information on how to use these protocols together to both authenticate a user and get authorization to access a protected resource, see [Microsoft identity platform and OAuth 2.0 SAML bearer assertion flow](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect versus SAML**: The platform uses both OpenID Connect and SAML to authenticate a user and enable single sign-on. SAML authentication is commonly used with identity providers such as Active Directory Federation Services (AD FS) federated to Azure AD, so it's often used in enterprise applications. OpenID Connect is commonly used for apps that are purely in the cloud, such as mobile apps, websites, and web APIs.

## Next steps

For other topics that cover authentication and authorization basics:

* To learn how access tokens, refresh tokens, and ID tokens are used in authorization and authentication, see [Security tokens](security-tokens.md).
* To learn about the process of registering your application so it can integrate with the Microsoft identity platform, see [Application model](application-model.md).