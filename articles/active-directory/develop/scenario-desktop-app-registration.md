---
title: Register desktop apps that call web APIs - Microsoft identity platform | Azure
description: Learn how to build a desktop app that calls web APIs (app registration)
services: active-directory
author: jmprieur
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
#Customer intent: As an application developer, I want to know how to write a desktop app that calls web APIs by using the Microsoft identity platform for developers.
---

# Desktop app that calls web APIs: App registration

This article covers the app registration specifics for a desktop application.

## Supported account types

The account types supported in a desktop application depend on the experience that you want to light up. Because of this relationship, the supported account types depend on the flows that you want to use.

### Audience for interactive token acquisition

If your desktop application uses interactive authentication, you can sign in users from any [account type](quickstart-register-app.md).

### Audience for desktop app silent flows

- To use Integrated Windows Authentication or a username and a password, your application needs to sign in users in your own tenant, for example, if you're a line-of-business (LOB) developer. Or, in Azure Active Directory organizations, your application needs to sign in users in your own tenant if it's an ISV scenario. These authentication flows aren't supported for Microsoft personal accounts.
- If you sign in users with social identities that pass a business-to-commerce (B2C) authority and policy, you can only use the interactive and username-password authentication.

## Redirect URIs

The redirect URIs to use in a desktop application depend on the flow you want to use.

- If you use interactive authentication or device code flow, use `https://login.microsoftonline.com/common/oauth2/nativeclient`. To achieve this configuration, select the corresponding URL in the **Authentication** section for your application.

  > [!IMPORTANT]
  > Today, MSAL.NET uses another redirect URI by default in desktop applications that run on Windows (`urn:ietf:wg:oauth:2.0:oob`). In the future, we'll want to change this default, so we recommend that you use `https://login.microsoftonline.com/common/oauth2/nativeclient`.

- If you build a native Objective-C or Swift app for macOS, register the redirect URI based on your application's bundle identifier in the following format: `msauth.<your.app.bundle.id>://auth`. Replace `<your.app.bundle.id>` with your application's bundle identifier.
- If your app uses only Integrated Windows Authentication or a username and a password, you don't need to register a redirect URI for your application. These flows do a round trip to the Microsoft identity platform v2.0 endpoint. Your application won't be called back on any specific URI.
- To distinguish [device code flow](scenario-desktop-acquire-token.md#device-code-flow), [Integrated Windows Authentication](scenario-desktop-acquire-token.md#integrated-windows-authentication), and a [username and a password](scenario-desktop-acquire-token.md#username-and-password) from a confidential client application using a client credential flow used in [daemon applications](scenario-daemon-overview.md), none of which requires a redirect URI, you need to configure it as a public client application. To achieve this configuration:

    1. In the [Azure portal](https://portal.azure.com), select your app in **App registrations**, and then select **Authentication**.
    1. In **Advanced settings** > **Default client type** > **Treat application as a public client**, select **Yes**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Enable public client setting on Authentication pane in Azure portal":::

## API permissions

Desktop applications call APIs for the signed-in user. They need to request delegated permissions. They can't request application permissions, which are handled only in [daemon applications](scenario-daemon-overview.md).

## Next steps

> [!div class="nextstepaction"]
> [Desktop app: App configuration](scenario-desktop-app-configuration.md)
