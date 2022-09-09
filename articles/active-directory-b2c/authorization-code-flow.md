---
title: Authorization code flow - Azure Active Directory B2C | Microsoft Docs
description: Learn how to build web apps by using Azure AD B2C and OpenID Connect authentication protocol.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
---

# OAuth 2.0 authorization code flow in Azure Active Directory B2C

You can use the OAuth 2.0 authorization code grant in apps installed on a device to gain access to protected resources, such as web APIs. By using the Azure Active Directory B2C (Azure AD B2C) implementation of OAuth 2.0, you can add sign-up, sign-in, and other identity management tasks to your mobile and desktop apps. This article is language-independent. In the article, we describe how to send and receive HTTP messages without using any open-source libraries.

The OAuth 2.0 authorization code flow is described in [section 4.1 of the OAuth 2.0 specification](https://tools.ietf.org/html/rfc6749). You can use it for authentication and authorization in most [application types](application-types.md), including web applications and natively installed applications. You can use the OAuth 2.0 authorization code flow to securely acquire access tokens and refresh tokens for your applications, which can be used to access resources that are secured by an [authorization server](protocols-overview.md).  The refresh token allows the client to acquire new access (and refresh) tokens once the access token expires, typically after one hour.

This article focuses on the **public clients** OAuth 2.0 authorization code flow. A public client is any client application that cannot be trusted to securely maintain the integrity of a secret password. This includes mobile apps, desktop applications, and essentially any application that runs on a device and needs to get access tokens.

> [!NOTE]
> To add identity management to a web app by using Azure AD B2C, use [OpenID Connect](openid-connect.md) instead of OAuth 2.0.

Azure AD B2C extends the standard OAuth 2.0 flows to do more than simple authentication and authorization. It introduces the [user flow](user-flow-overview.md). With user flows, you can use OAuth 2.0 to add user experiences to your application, such as sign-up, sign-in, and profile management. Identity providers that use the OAuth 2.0 protocol include [Amazon](identity-provider-amazon.md), [Azure Active Directory](identity-provider-azure-ad-single-tenant.md), [Facebook](identity-provider-facebook.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), and [LinkedIn](identity-provider-linkedin.md).

To try the HTTP requests in this article:

1. Replace `{tenant}` with the name of your Azure AD B2C tenant.
1. Replace `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` with the app ID of an application you've previously registered in your Azure AD B2C tenant.
1. Replace `{policy}` with the name of a policy you've created in your tenant, for example `b2c_1_sign_in`.

## 1. Get an authorization code
The authorization code flow begins with the client directing the user to the `/authorize` endpoint. This is the interactive part of the flow, where the user takes action. In this request, the client indicates in the `scope` parameter the permissions that it needs to acquire from the user. The following three examples (with line breaks for readability) each use a different user flow.


```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parameter | Required? | Description |
| --- | --- | --- |
|{tenant}| Required | Name of your Azure AD B2C tenant|
| {policy} | Required | The user flow to be run. Specify the name of a user flow you've created in your Azure AD B2C tenant. For example: `b2c_1_sign_in`, `b2c_1_sign_up`, or `b2c_1_edit_profile`. |
| client_id |Required |The application ID assigned to your app in the [Azure portal](https://portal.azure.com). |
| response_type |Required |The response type, which must include `code` for the authorization code flow. |
| redirect_uri |Required |The redirect URI of your app, where authentication responses are sent and received by your app. It must exactly match one of the redirect URIs that you registered in the portal, except that it must be URL-encoded. |
| scope |Required |A space-separated list of scopes. A single scope value indicates to Azure Active Directory (Azure AD) both of the permissions that are being requested. Using the client ID as the scope indicates that your app needs an access token that can be used against your own service or web API, represented by the same client ID.  The `offline_access` scope indicates that your app needs a refresh token for long-lived access to resources. You also can use the `openid` scope to request an ID token from Azure AD B2C. |
| response_mode |Recommended |The method that you use to send the resulting authorization code back to your app. It can be `query`, `form_post`, or `fragment`. |
| state |Recommended |A value included in the request that can be a string of any content that you want to use. Usually, a randomly generated unique value is  used, to prevent cross-site request forgery attacks. The state also is used to encode information about the user's state in the app before the authentication request occurred. For example, the page the user was on, or the user flow that was being executed. |
| prompt |Optional |The type of user interaction that is required. Currently, the only valid value is `login`, which forces the user to enter their credentials on that request. Single sign-on will not take effect. |

At this point, the user is asked to complete the user flow's workflow. This might involve the user entering their username and password, signing in with a social identity, signing up for the directory, or any other number of steps. User actions depend on how the user flow is defined.

After the user completes the user flow, Azure AD returns a response to your app at the value you used for `redirect_uri`. It uses the method specified in the `response_mode` parameter. The response is exactly the same for each of the user action scenarios, independent of the user flow that was executed.

A successful response that uses `response_mode=query` looks like this:

```http
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Description |
| --- | --- |
| code |The authorization code that the app requested. The app can use the authorization code to request an access token for a target resource. Authorization codes are very short-lived. Typically, they expire after about 10 minutes. |
| state |See the full description in the table in the preceding section. If a `state` parameter is included in the request, the same value should appear in the response. The app should verify that the `state` values in the request and response are identical. |

Error responses also can be sent to the redirect URI so that the app can handle them appropriately:

```http
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Description |
| --- | --- |
| error |An error code string that you can use to classify the types of errors that occur. You also can use the string to react to errors. |
| error_description |A specific error message that can help you identify the root cause of an authentication error. |
| state |See the full description in the preceding table. If a `state` parameter is included in the request, the same value should appear in the response. The app should verify that the `state` values in the request and response are identical. |

## 2. Get a token
Now that you've acquired an authorization code, you can redeem the `code` for a token to the intended resource by sending a POST request to the `/token` endpoint. In Azure AD B2C, you can [request access tokens for other API's](access-tokens.md#request-a-token) as usual by specifying their scope(s) in the request.

You can also request an access token for your app's own back-end Web API by convention of using the app's client ID as the requested scope (which will result in an access token with that client ID as the "audience"):

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Required? | Description |
| --- | --- | --- |
|{tenant}| Required | Name of your Azure AD B2C tenant|
|{policy}| Required| The user flow that was used to acquire the authorization code. You cannot use a different user flow in this request. |
| client_id |Required |The application ID assigned to your app in the [Azure portal](https://portal.azure.com).|
| client_secret | Yes, in Web Apps | The application secret that was generated in the [Azure portal](https://portal.azure.com/). Client secrets are used in this flow for Web App scenarios, where the client can securely store a client secret. For Native App (public client) scenarios, client secrets cannot be securely stored, and therefore are not used in this call. If you use a client secret, please change it on a periodic basis. |
| grant_type |Required |The type of grant. For the authorization code flow, the grant type must be `authorization_code`. |
| scope |Recommended |A space-separated list of scopes. A single scope value indicates to Azure AD both of the permissions that are being requested. Using the client ID as the scope indicates that your app needs an access token that can be used against your own service or web API, represented by the same client ID.  The `offline_access` scope indicates that your app needs a refresh token for long-lived access to resources.  You also can use the `openid` scope to request an ID token from Azure AD B2C. |
| code |Required |The authorization code that you acquired in the first leg of the flow. |
| redirect_uri |Required |The redirect URI of the application where you received the authorization code. |

A successful token response looks like this:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parameter | Description |
| --- | --- |
| not_before |The time at which the token is considered valid, in epoch time. |
| token_type |The token type value. The only type that Azure AD supports is Bearer. |
| access_token |The signed JSON Web Token (JWT) that you requested. |
| scope |The scopes that the token is valid for. You also can use scopes to cache tokens for later use. |
| expires_in |The length of time that the token is valid (in seconds). |
| refresh_token |An OAuth 2.0 refresh token. The app can use this token to acquire additional tokens after the current token expires. Refresh tokens are long-lived. You can use them to retain access to resources for extended periods of time. For more information, see the [Azure AD B2C token reference](tokens-overview.md). |

Error responses look like this:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Description |
| --- | --- |
| error |An error code string that you can use to classify the types of errors that occur. You also can use the string to react to errors. |
| error_description |A specific error message that can help you identify the root cause of an authentication error. |

## 3. Use the token
Now that you've successfully acquired an access token, you can use the token in requests to your back-end web APIs by including it in the `Authorization` header:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4. Refresh the token
Access tokens and ID tokens are short-lived. After they expire, you must refresh them to continue to access resources. To do this, submit another POST request to the `/token` endpoint. This time, provide the `refresh_token` instead of the `code`:

```http
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Required? | Description |
| --- | --- | --- |
|{tenant}| Required | Name of your Azure AD B2C tenant|
|{policy} |Required |The user flow that was used to acquire the original refresh token. You cannot use a different user flow in this request. |
| client_id |Required |The application ID assigned to your app in the [Azure portal](https://portal.azure.com). |
| client_secret | Yes, in Web Apps | The application secret that was generated in the [Azure portal](https://portal.azure.com/). Client secrets are used in this flow for Web App scenarios, where the client can securely store a client secret. For Native App (public client) scenarios, client secrets cannot be securely stored, and therefore are not used in this call. If you use a client secret, please change it on a periodic basis. |
| grant_type |Required |The type of grant. For this leg of the authorization code flow, the grant type must be `refresh_token`. |
| scope |Recommended |A space-separated list of scopes. A single scope value indicates to Azure AD both of the permissions that are being requested. Using the client ID as the scope indicates that your app needs an access token that can be used against your own service or web API, represented by the same client ID.  The `offline_access` scope indicates that your app will need a refresh token for long-lived access to resources.  You also can use the `openid` scope to request an ID token from Azure AD B2C. |
| redirect_uri |Optional |The redirect URI of the application where you received the authorization code. |
| refresh_token |Required |The original refresh token that you acquired in the second leg of the flow. |

A successful token response looks like this:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parameter | Description |
| --- | --- |
| not_before |The time at which the token is considered valid, in epoch time. |
| token_type |The token type value. The only type that Azure AD supports is Bearer. |
| access_token |The signed JWT that you requested. |
| scope |The scopes that the token is valid for. You also can use the scopes to cache tokens for later use. |
| expires_in |The length of time that the token is valid (in seconds). |
| refresh_token |An OAuth 2.0 refresh token. The app can use this token to acquire additional tokens after the current token expires. Refresh tokens are long-lived, and can be used to retain access to resources for extended periods of time. For more information, see the [Azure AD B2C token reference](tokens-overview.md). |

Error responses look like this:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Description |
| --- | --- |
| error |An error code string that you can use to classify types of errors that occur. You also can use the string to react to errors. |
| error_description |A specific error message that can help you identify the root cause of an authentication error. |

## Use your own Azure AD B2C directory
To try these requests yourself, complete the following steps. Replace the example values we used in this article with your own values.

1. [Create an Azure AD B2C directory](tutorial-create-tenant.md). Use the name of your directory in the requests.
2. [Create an application](tutorial-register-applications.md) to obtain an application ID and a redirect URI. Include a native client in your app.
3. [Create your user flows](user-flow-overview.md) to obtain your user flow names.
