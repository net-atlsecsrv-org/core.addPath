---
title: Customize browsers and WebViews 
titleSuffix: Microsoft identity platform
description: Learn how to customize the browser experience used by MSAL for iOS and macOS to sign in users
services: active-directory
author: tylermsft
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
---

# How to: Customize browsers and WebViews for iOS/macOS

A web browser is required for interactive authentication. On iOS, the Microsoft Authentication Library (MSAL) uses the system web browser by default (which might appear on top of your app) to do interactive authentication to sign in users. Using the system browser has the advantage of sharing the Single Sign ON (SSO) state with other applications and with web applications.

You can change the experience by customizing the configuration to other options for displaying web content, such as:

For iOS only:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

For iOS and macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL for macOS only supports `WKWebView`.

## System browsers

For iOS, `ASWebAuthenticationSession`, `SFAuthenticationSession`, and `SFSafariViewController` are considered system browsers. In general, system browsers share cookies and other website data with the Safari browser application.

By default, MSAL will dynamically detect iOS version and select the recommended system browser available on that version. On iOS 12+ it will be `ASWebAuthenticationSession`. 

| Version | Web browser |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Developers can also select a different system browser for MSAL apps:

- `SFAuthenticationSession` is the iOS 11 version of `ASWebAuthenticationSession`.
- `SFSafariViewController` is more general purpose and provides an interface for browsing the web and can be used for login purposes as well. In iOS 9 and 10, cookies and other website data are shared with Safari--but not in iOS 11 and later.

## In-app browser

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) is an in-app browser that displays web content. It doesn't share cookies or web site data with other **WKWebView** instances, or with the Safari browser. WKWebView is a cross-platform browser that is available for both iOS and macOS.

## Cookie sharing and Single sign-on (SSO) implications

The browser you use impacts the SSO experience because of how they share cookies. The following tables summarize the SSO experiences per browser.

| Technology    | Browser Type  | iOS availability | macOS availability | Shares cookies and other data  | MSAL availability | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System | iOS12 and up | macOS 10.15 and up | Yes | iOS only | w/ Safari instances
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System | iOS11 and up | N/A | Yes | iOS only |  w/ Safari instances
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System | iOS11 and up | N/A | No | iOS only | No**
| **SFSafariViewController** | System | iOS10 | N/A | Yes | iOS only |  w/ Safari instances
| **WKWebView**  | In-app | iOS8 and up | macOS 10.10 and up | No | iOS and macOS | No**

** For SSO to work, tokens need to be shared between apps. This requires a token cache, or broker application, such as Microsoft Authenticator for iOS.

## Change the default browser for the request

You can use an in-app browser, or a specific system browser depending on your UX requirements, by changing the following property in `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## Change per interactive request

Each request can be configured to override the default browser by changing the `MSALInteractiveTokenParameters.webviewParameters.webviewType` property before passing it to the `acquireTokenWithParameters:completionBlock:` API.

Additionally, MSAL supports passing in a custom `WKWebView` by setting the `MSALInteractiveTokenParameters.webviewParameters.customWebView` property.

For example:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

If you use a custom webview, notifications are used to indicate the status of the web content being displayed, such as:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### Options

All MSAL supported web browser types are declared in the [MSALWebviewType enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## Next steps

Learn more about [Authentication flows and application scenarios](authentication-flows-app-scenarios.md)
