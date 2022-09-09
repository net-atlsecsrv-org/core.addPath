---
title: Microsoft Enterprise SSO plug-in for Apple devices
titleSuffix: Microsoft identity platform | Azure
description: Learn about the Azure Active Directory SSO plug-in for iOS, iPadOS, and macOS devices.
services: active-directory
author: brandwe
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
---

# Microsoft Enterprise SSO plug-in for Apple devices (preview)

>[!IMPORTANT]
> This feature [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

The *Microsoft Enterprise SSO plug-in for Apple devices* provides single sign-on (SSO) for Azure Active Directory (Azure AD) accounts on macOS, iOS, and iPadOS across all applications that support Apple's [enterprise single sign-on](https://developer.apple.com/documentation/authenticationservices) feature. The plug-in provides SSO for even old applications that your business might depend on but that don't yet support the latest identity libraries or protocols. Microsoft worked closely with Apple to develop this plug-in to increase your application's usability while providing the best protection available.

The Enterprise SSO plug-in is currently a built-in feature of the following apps:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPadOS
* Microsoft Intune [Company Portal](/mem/intune/apps/apps-company-portal-macos): macOS

## Features

The Microsoft Enterprise SSO plug-in for Apple devices offers the following benefits:

- It provides SSO for Azure AD accounts across all applications that support the Apple Enterprise SSO feature.
- It can be enabled by any mobile device management (MDM) solution.
- It extends SSO to applications that don't yet use Microsoft identity platform libraries.
- It extends SSO to applications that use OAuth 2, OpenID Connect, and SAML.

## Requirements

To use the Microsoft Enterprise SSO plug-in for Apple devices:

- The device must *support* and have an installed app that has the Microsoft Enterprise SSO plug-in for Apple devices:
  - iOS 13.0 and later: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 and later: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 and later: [Intune Company Portal app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- The device must be *enrolled in MDM*, for example, through Microsoft Intune.
- Configuration must be *pushed to the device* to enable the Enterprise SSO plug-in. Apple requires this security constraint.

### iOS requirements:
- iOS 13.0 or higher must be installed on the device.
- A Microsoft application that provides the Microsoft Enterprise SSO plug-in for Apple devices must be installed on the device. For Public Preview, these applications are the [Microsoft Authenticator app](/azure/active-directory/user-help/user-help-auth-app-overview).


### macOS requirements:
- macOS 10.15 or higher must be installed on the device. 
- A Microsoft application that provides the Microsoft Enterprise SSO plug-in for Apple devices must be installed on the device. For Public Preview, these applications include the [Intune Company Portal app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## Enable the SSO plug-in

Use the following information to enable the SSO plug-in by using MDM.
### Microsoft Intune configuration

If you use Microsoft Intune as your MDM service, you can use built-in configuration profile settings to enable the Microsoft Enterprise SSO plug-in:

1. Configure the [SSO app extension](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) settings of a configuration profile. 
1. If the profile isn't already assigned, [assign the profile to a user or device group](/mem/intune/configuration/device-profile-assign).

The profile settings that enable the SSO plug-in are automatically applied to the group's devices the next time each device checks in with Intune.

### Manual configuration for other MDM services

If you don't use Intune for MDM, use the following parameters to configure the Microsoft Enterprise SSO plug-in for Apple devices.

iOS settings:

- **Extension ID**: `com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: This field isn't needed for iOS.

macOS settings:

- **Extension ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Team ID**: `UBF8T346G9`

Common settings:

- **Type**: Redirect
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### More configuration options
You can add more configuration options to extend SSO functionality to other apps.

#### Enable SSO for apps that don't use a Microsoft identity platform library

The SSO plug-in allows any application to participate in SSO even if it wasn't developed by using a Microsoft SDK like Microsoft Authentication Library (MSAL).

The SSO plug-in is installed automatically by devices that have:
* Downloaded the Authenticator app on iOS or iPadOS, or downloaded the Intune Company Portal app on macOS.
* Registered their device with your organization. 

Your organization likely uses the Authenticator app for scenarios like multifactor authentication (MFA), passwordless authentication, and conditional access. By using an MDM provider, you can turn on the SSO plug-in for your applications. Microsoft has made it easy to configure the plug-in inside the Microsoft Endpoint Manager in Intune. An allowlist is used to configure these applications to use the SSO plug-in.

>[!IMPORTANT]
> The Microsoft Enterprise SSO plug-in supports only apps that use native Apple network technologies or webviews. It doesn't support applications that ship their own network layer implementation.  

Use the following parameters to configure the Microsoft Enterprise SSO plug-in for apps that don't use a Microsoft identity platform library.

To provide a list of specific apps, use these parameters:

- **Key**: `AppAllowList`
- **Type**: `String`
- **Value**: Comma-delimited list of application bundle IDs for the applications that are allowed to participate in SSO.
- **Example**: `com.contoso.workapp, com.contoso.travelapp`

To provide a list of prefixes, use these parameters:
- **Key**: `AppPrefixAllowList`
- **Type**: `String`
- **Value**: Comma-delimited list of application bundle ID prefixes for the applications that are allowed to participate in SSO. This parameter allows all apps that start with a particular prefix to participate in SSO.
- **Example**: `com.contoso., com.fabrikam.`

[Consented apps](./application-consent-experience.md) that the MDM admin allows to participate in SSO can silently get a token for the end user. So add only trusted applications to the allowlist. 

>[!NOTE]
> You don't need to add applications that use MSAL or ASWebAuthenticationSession to the list of apps that can participate in SSO. Those applications are enabled by default. 

##### Find app bundle identifiers on iOS devices

Apple provides no easy way to get bundle IDs from the App Store. The easiest way to get the bundle IDs of the apps you want to use for SSO is to ask your vendor or app developer. If that option isn't available, you can use your MDM configuration to find the bundle IDs: 

1. Temporarily enable the following flag in your MDM configuration:

    - **Key**: `admin_debug_mode_enabled`
    - **Type**: `Integer`
    - **Value**: 1 or 0
1. When this flag is on, sign in to iOS apps on the device for which you want to know the bundle ID. 
1. In the Authenticator app, select **Help** > **Send logs** > **View logs**. 
1. In the log file, look for following line: `[ADMIN MODE] SSO extension has captured following app bundle identifiers`. This line should capture all application bundle IDs that are visible to the SSO extension. 

Use the bundle IDs to configure SSO for the apps. 

#### Allow users to sign in from unknown applications and the Safari browser

By default, the Microsoft Enterprise SSO plug-in provides SSO for authorized apps only when a user has signed in from an app that uses a Microsoft identity platform library like MSAL or Azure Active Directory Authentication Library (ADAL). The Microsoft Enterprise SSO plug-in can also acquire a shared credential when it's called by another app that uses a Microsoft identity platform library during a new token acquisition.

When you enable the `browser_sso_interaction_enabled` flag, apps that don't use a Microsoft identity platform library can do the initial bootstrapping and get a shared credential. The Safari browser can also do the initial bootstrapping and get a shared credential. 

If the Microsoft Enterprise SSO plug-in doesn't have a shared credential yet, it will try to get one whenever a sign-in is requested from an Azure AD URL inside the Safari browser, ASWebAuthenticationSession, SafariViewController, or another permitted native application. 

Use these parameters to enable the flag: 

- **Key**: `browser_sso_interaction_enabled`
- **Type**: `Integer`
- **Value**: 1 or 0

macOS requires this setting so it can provide a consistent experience across all apps. iOS and iPadOS don't require this setting because most apps use the Authenticator application for sign-in. But we recommend that you enable this setting because if some of your applications don't use the Authenticator app on iOS or iPadOS, this flag will improve the experience. The setting is disabled by default.

#### Disable asking for MFA during initial bootstrapping

By default, the Microsoft Enterprise SSO plug-in always prompts the user for MFA during the initial bootstrapping and while getting a shared credential. The user is prompted for MFA even if it's not required for the application that the user has opened. This behavior allows the shared credential to be easily used across all other applications without the need to prompt the user if MFA is required later. Because the user gets fewer prompts overall, this setup is generally a good decision.

Enabling `browser_sso_disable_mfa` turns off MFA during initial bootstrapping and while getting the shared credential. In this case, the user is prompted only when MFA is required by an application or resource. 

To enable the flag, use these parameters:

- **Key**: `browser_sso_disable_mfa`
- **Type**: `Integer`
- **Value**: 1 or 0

We recommend keeping this flag disabled because it reduces the number of times the user is prompted to sign in. If your organization rarely uses MFA, you might want to enable the flag. But we recommend that you use MFA more frequently instead. For this reason, the flag is disabled by default.

#### Disable OAuth 2 application prompts

The Microsoft Enterprise SSO plug-in provides SSO by appending shared credentials to network requests that come from allowed applications. However, some OAuth 2 applications might incorrectly enforce end-user prompts at the protocol layer. If you see this problem, you'll also see that shared credentials are ignored for those apps. Your user is prompted to sign in even though the Microsoft Enterprise SSO plug-in works for other applications.  

Enabling the `disable_explicit_app_prompt` flag restricts the ability of both native applications and web applications to force an end-user prompt on the protocol layer and bypass SSO. To enable the flag, use these parameters:

- **Key**: `disable_explicit_app_prompt`
- **Type**: `Integer`
- **Value**: 1 or 0

We recommend enabling this flag to get a consistent experience across all apps. It's disabled by default. 

#### Enable SSO through cookies for a specific application

A few apps might be incompatible with the SSO extension. Specifically, apps that have advanced network settings might experience unexpected issues when they're enabled for SSO. For example, you might see an error indicating that network request was canceled or interrupted. 

If you have problems signing in by using the method described in the [Applications that don't use MSAL](#applications-that-dont-use-msal) section, try an alternative configuration. Use these parameters to configure the plug-in:

- **Key**: `AppCookieSSOAllowList`
- **Type**: `String`
- **Value**: Comma-delimited list of application bundle ID prefixes for the applications that are allowed to participate in the SSO. All apps that start with the listed prefixes will be allowed to participate in SSO.
- **Example**: `com.contoso.myapp1, com.fabrikam.myapp2`

Applications enabled for the SSO by using this setup need to be added to both `AppCookieSSOAllowList` and `AppPrefixAllowList`.

Try this configuration only for applications that have unexpected sign-in failures. 

#### Use Intune for simplified configuration

You can use Intune as your MDM service to ease configuration of the Microsoft Enterprise SSO plug-in. For example, you can use Intune to enable the plug-in and add old apps to an allowlist so they get SSO. 

For more information, see the [Intune configuration documentation](/intune/configuration/ios-device-features-settings).

## Use the SSO plug-in in your application

[MSAL for Apple devices](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versions 1.1.0 and later supports the Microsoft Enterprise SSO plug-in for Apple devices. It's the recommended way to add support for the Microsoft Enterprise SSO plug-in. It ensures you get the full capabilities of the Microsoft identity platform.

If you're building an application for frontline-worker scenarios, see [Shared device mode for iOS devices](msal-ios-shared-devices.md) for setup information.

## Understand how the SSO plug-in works

The Microsoft Enterprise SSO plug-in relies on the [Apple Enterprise SSO framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Identity providers that join the framework can intercept network traffic for their domains and enhance or change how those requests are handled. For example, the SSO plug-in can show more UIs to collect end-user credentials securely, require MFA, or silently provide tokens to the application.

Native applications can also implement custom operations and communicate directly with the SSO plug-in. For more information, see this [2019 Worldwide Developer Conference video from Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### Applications that use MSAL

[MSAL for Apple devices](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versions 1.1.0 and later supports the Microsoft Enterprise SSO plug-in for Apple devices natively for work and school accounts. 

You don't need any special configuration if you followed [all recommended steps](./quickstart-v2-ios.md) and used the default [redirect URI format](./redirect-uris-ios.md). On devices that have the SSO plug-in, MSAL automatically invokes it for all interactive and silent token requests. It also invokes it for account enumeration and account removal operations. Because MSAL implements a native SSO plug-in protocol that relies on custom operations, this setup provides the smoothest native experience to the end user. 

If the SSO plug-in isn't enabled by MDM but the Microsoft Authenticator app is present on the device, MSAL instead uses the Authenticator app for any interactive token requests. The SSO plug-in shares SSO with the Authenticator app.

### Applications that don't use MSAL

Applications that don't use a Microsoft identity platform library, like MSAL, can still get SSO if an administrator adds these applications to the allowlist. 

You don't need to change the code in those apps as long as the following conditions are satisfied:

- The application uses Apple frameworks to run network requests. These frameworks include [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) and [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession), for example. 
- The application uses standard protocols to communicate with Azure AD. These protocols include, for example, OAuth 2, SAML, and WS-Federation.
- The application doesn't collect plaintext usernames and passwords in the native UI.

In this case, SSO is provided when the application creates a network request and opens a web browser to sign the user in. When a user is redirected to an Azure AD sign-in URL, the SSO plug-in validates the URL and checks for an SSO credential for that URL. If it finds the credential, the SSO plug-in passes it to Azure AD, which authorizes the application to complete the network request without asking the user to enter credentials. Additionally, if the device is known to Azure AD, the SSO plug-in passes the device certificate to satisfy the device-based conditional access check. 

To support SSO for non-MSAL apps, the SSO plug-in implements a protocol similar to the Windows browser plug-in described in [What is a primary refresh token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Compared to MSAL-based apps, the SSO plug-in acts more transparently for non-MSAL apps. It integrates with the existing browser sign-in experience that apps provide. 

The end user sees the familiar experience and doesn't have to sign in again in each application. For example, instead of displaying the native account picker, the SSO plug-in adds SSO sessions to the web-based account picker experience. 

## Next steps

Learn about [Shared device mode for iOS devices](msal-ios-shared-devices.md).
