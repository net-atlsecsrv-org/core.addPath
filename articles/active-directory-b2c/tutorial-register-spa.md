---
title: "Tutorial: Register a single-page application"
titleSuffix: Azure AD B2C
description: Follow this tutorial to learn how to register a single-page application (SPA) in Azure Active Directory B2C using the Azure portal.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code 
ms.author: mimart
ms.subservice: B2C
---

# Tutorial: Register a single-page application (SPA) in Azure Active Directory B2C

Before your [applications](application-types.md) can interact with Azure Active Directory B2C (Azure AD B2C), they must be registered in a tenant that you manage. This tutorial shows you how to register a single-page application ("SPA") using the Azure portal.

## Overview of authentication options

Many modern web applications are built as client-side single-page applications ("SPAs"). Developers write them by using JavaScript or a SPA framework such as Angular, Vue, and React. These applications run on a web browser and have different authentication characteristics than traditional server-side web applications.

Azure AD B2C provides **two** options to enable single-page applications to sign in users and get tokens to access back-end services or web APIs:

### Authorization code flow (with PKCE)
- [OAuth 2.0 Authorization code flow (with PKCE)](./authorization-code-flow.md). The authorization code flow allows the application to exchange an authorization code for **ID** tokens to represent the authenticated user and **Access** tokens needed to call protected APIs. In addition, it returns **Refresh** tokens that provide long-term access to resources on behalf of users without requiring interaction with those users. 

This is the **recommended** approach. Having limited-lifetime refresh tokens helps your application adapt to [modern browser cookie privacy limitations](../active-directory/develop/reference-third-party-cookies-spas.md), like Safari ITP.

To take advantage of this flow, your application can use an authentication library that supports it, like [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa). 

![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg)

### Implicit grant flow
- [OAuth 2.0 implicit flow](implicit-flow-single-page-application.md). Some frameworks, like [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), only support the implicit grant flow. The implicit grant flow allows the application to get **ID** and **Access** tokens. Unlike the authorization code flow, implicit grant flow does not return a **Refresh token**. 

![Single-page applications-implicit](./media/tutorial-single-page-app/spa-app.svg)

This authentication flow does not include application scenarios that use cross-platform JavaScript frameworks such as Electron and React-Native. Those scenarios require further capabilities for interaction with the native platforms.

## Prerequisites

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

If you haven't already created your own [Azure AD B2C Tenant](tutorial-create-tenant.md), create one now. You can use an existing Azure AD B2C tenant.

## Register the SPA application

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select the **Directory + Subscription** icon in the portal toolbar, and then select the directory that contains your Azure AD B2C tenant.
1. In the Azure portal, search for and select **Azure AD B2C**.
1. Select **App registrations**, and then select **New registration**.
1. Enter a **Name** for the application. For example, *spaapp1*.
1. Under **Supported account types**, select **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**
1. Under **Redirect URI**, select **Single-page application (SPA)**, and then enter `https://jwt.ms` in the URL text box.

    The redirect URI is the endpoint to which the user is sent by the authorization server (Azure AD B2C, in this case) after completing its interaction with the user, and to which an access token or authorization code is sent upon successful authorization. In a production application, it's typically a publicly accessible endpoint where your app is running, like `https://contoso.com/auth-response`. For testing purposes like this tutorial, you can set it to `https://jwt.ms`, a Microsoft-owned web application that displays the decoded contents of a token (the contents of the token never leave your browser). During app development, you might add the endpoint where your application listens locally, like `http://localhost:5000`. You can add and modify redirect URIs in your registered applications at any time.

    The following restrictions apply to redirect URIs:

    * The reply URL must begin with the scheme `https`, unless using `localhost`.
    * The reply URL is case-sensitive. Its case must match the case of the URL path of your running application. For example, if your application includes as part of its path `.../abc/response-oidc`,  do not specify `.../ABC/response-oidc` in the reply URL. Because the web browser treats paths as case-sensitive, cookies associated with `.../abc/response-oidc` may be excluded if redirected to the case-mismatched `.../ABC/response-oidc` URL.

1. Under **Permissions**, select the *Grant admin consent to openid and offline_access permissions* check box.
1. Select **Register**.


## Enable the implicit flow
If using the implicit flow, you need to enable the implicit grant flow in the app registration.

1. In the left menu, under **Manage**, select **Authentication**.
1. Under **Implicit grant**, select both the **Access tokens** and **ID tokens** check boxes.
1. Select **Save**.

## Migrate from the implicit flow

If you have an existing application that uses the implicit flow, we recommend migrating to using the authorization code flow by using a framework that supports it, like [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

When all your production single-page applications represented by an app registration are using the authorization code flow, disable the implicit grant flow settings. 

1. In the left menu, under **Manage**, select **Authentication**.
1. Under **Implicit grant**, de-select both the **Access tokens** and **ID tokens** check boxes.
1. Select **Save**.

Applications using the implicit flow can continue to function if you leave the implicit flow enabled (checked).

* * *

## Next steps

Next, learn how to create user flows to enable your users to sign up, sign in, and manage their profiles.

> [!div class="nextstepaction"]
> [Create user flows in Azure Active Directory B2C >](tutorial-create-user-flows.md)
