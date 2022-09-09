---
title: Microsoft identity platform and OAuth2.0 On-Behalf-Of flow | Azure
titleSuffix: Microsoft identity platform
description: This article describes how to use HTTP messages to implement service to service authentication using the OAuth2.0 On-Behalf-Of flow.
services: active-directory
author: hpsin
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
---

# Microsoft identity platform and OAuth 2.0 On-Behalf-Of flow


The OAuth 2.0 On-Behalf-Of flow (OBO) serves the use case where an application invokes a service/web API, which in turn needs to call another service/web API. The idea is to propagate the delegated user identity and permissions through the request chain. For the middle-tier service to make authenticated requests to the downstream service, it needs to secure an access token from the Microsoft identity platform, on behalf of the user.

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> As of May 2018, some implicit-flow derived `id_token` can't be used for OBO flow. Single-page apps (SPAs) should pass an **access** token to a middle-tier confidential client to perform OBO flows instead. For more info about which clients can perform OBO calls, see [limitations](#client-limitations).

## Protocol diagram

Assume that the user has been authenticated on an application using the [OAuth 2.0 authorization code grant flow](v2-oauth2-auth-code-flow.md) or another login flow. At this point, the application has an access token *for API A* (token A) with the user's claims and consent to access the middle-tier web API (API A). Now, API A needs to make an authenticated request to the downstream web API (API B).

The steps that follow constitute the OBO flow and are explained with the help of the following diagram.

![Shows the OAuth2.0 On-Behalf-Of flow](./media/v2-oauth2-on-behalf-of-flow/protocols-oauth-on-behalf-of-flow.png)

1. The client application makes a request to API A with token A (with an `aud` claim of API A).
1. API A authenticates to the Microsoft identity platform token issuance endpoint and requests a token to access API B.
1. The Microsoft identity platform token issuance endpoint validates API A's credentials along with token A and issues the access token for API B (token B) to API A.
1. Token B is set by API A in the authorization header of the request to API B.
1. Data from the secured resource is returned by API B to API A, and from there to the client.

> [!NOTE]
> In this scenario, the middle-tier service has no user interaction to obtain the user's consent to access the downstream API. Therefore, the option to grant access to the downstream API is presented upfront as a part of the consent step during authentication. To learn how to set this up for your app, see [Gaining consent for the middle-tier application](#gaining-consent-for-the-middle-tier-application).

## Middle-tier access token request

To request an access token, make an HTTP POST to the tenant-specific Microsoft identity platform token endpoint with the following parameters.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

There are two cases depending on whether the client application chooses to be secured by a shared secret or a certificate.

### First case: Access token request with a shared secret

When using a shared secret, a service-to-service access token request contains the following parameters:

| Parameter | Type | Description |
| --- | --- | --- |
| `grant_type` | Required | The type of  token request. For a request using a JWT, the value must be `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Required | The application (client) ID that [the Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page has assigned to your app. |
| `client_secret` | Required | The client secret that you generated for your app in the Azure portal - App registrations page. |
| `assertion` | Required | The access token that was sent to the middle-tier API.  This token must have an audience (`aud`) claim of the app making this OBO request (the app denoted by the `client-id` field). Applications cannot redeem a token for a different app (so e.g. if a client sends an API a token meant for MS Graph, the API cannot redeem it using OBO.  It should instead reject the token).  |
| `scope` | Required | A space separated list of scopes for the token request. For more information, see [scopes](v2-permissions-and-consent.md). |
| `requested_token_use` | Required | Specifies how the request should be processed. In the OBO flow, the value must be set to `on_behalf_of`. |

#### Example

The following HTTP POST requests an access token and refresh token with `user.read` scope for the https://graph.microsoft.com web API.

```HTTP
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyO{a lot of characters here}
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### Second case: Access token request with a certificate

A service-to-service access token request with a certificate contains the following parameters:

| Parameter | Type | Description |
| --- | --- | --- |
| `grant_type` | Required | The type of the token request. For a request using a JWT, the value must be `urn:ietf:params:oauth:grant-type:jwt-bearer`. |
| `client_id` | Required |  The application (client) ID that [the Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page has assigned to your app. |
| `client_assertion_type` | Required | The value must be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| `client_assertion` | Required | An assertion (a JSON web token) that you need to create and sign with the certificate you registered as credentials for your application. To learn how to register your certificate and the format of the assertion, see [certificate credentials](active-directory-certificate-credentials.md). |
| `assertion` | Required |  The access token that was sent to the middle-tier API.  This token must have an audience (`aud`) claim of the app making this OBO request (the app denoted by the `client-id` field). Applications cannot redeem a token for a different app (so e.g. if a client sends an API a token meant for MS Graph, the API cannot redeem it using OBO.  It should instead reject the token).  |
| `requested_token_use` | Required | Specifies how the request should be processed. In the OBO flow, the value must be set to `on_behalf_of`. |
| `scope` | Required | A space-separated list of scopes for the token request. For more information, see [scopes](v2-permissions-and-consent.md).|

Notice that the parameters are almost the same as in the case of the request by shared secret except that the `client_secret` parameter is replaced by two parameters: a `client_assertion_type` and `client_assertion`.

#### Example

The following HTTP POST requests an access token with `user.read` scope for the https://graph.microsoft.com web API with a certificate.

```HTTP
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com/<tenant>
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiO{a lot of characters here}
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## Middle-tier access token response

A success response is a JSON OAuth 2.0 response with the following parameters.

| Parameter | Description |
| --- | --- |
| `token_type` | Indicates the token type value. The only type that Microsoft identity platform supports is `Bearer`. For more info about bearer tokens, see the [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| `scope` | The scope of access granted in the token. |
| `expires_in` | The length of time, in seconds, that the access token is valid. |
| `access_token` | The requested access token. The calling service can use this token to authenticate to the receiving service. |
| `refresh_token` | The refresh token for the requested access token. The calling service can use this token to request another access token after the current access token expires. The refresh token is only provided if the `offline_access` scope was requested. |

### Success response example

The following example shows a success response to a request for an access token for the https://graph.microsoft.com web API.

```json
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4An{a lot of characters here}"
}
```

> [!NOTE]
> The above access token is a v1.0-formatted token for Microosft Graph. This is because the token format is based on the **resource** being accessed and unrelated to the endpoints used to request it. The Microsoft Graph is setup to accept v1.0 tokens, so Microsoft identity platform produces v1.0 access tokens when a client requests tokens for Microsoft Graph. Other apps may indicate that they want v2.0-format tokens, v1.0-format tokens, or even proprietary or encrypted token formats.  Both the v1.0 and v2.0 endpoints can emit either format of token - this way the resource can always get the right format of token regardless of how or where the token was requested by the client. 
>
> Only applications should look at access tokens. Clients **must not** inspect them. Inspecting access tokens for other apps in your code will result in your app unexpectedly breaking when that app changes the format of their tokens or starts encrypting them. 

### Error response example

An error response is returned by the token endpoint when trying to acquire an access token for the downstream API, if the downstream API has a Conditional Access policy (such as [multi-factor authentication](../authentication/concept-mfa-howitworks.md)) set on it. The middle-tier service should surface this error to the client application so that the client application can provide the user interaction to satisfy the Conditional Access policy.

```json
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## Use the access token to access the secured resource

Now the middle-tier service can use the token acquired above to make authenticated requests to the downstream web API, by setting the token in the `Authorization` header.

### Example

```HTTP
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## SAML assertions obtained with an OAuth2.0 OBO flow

Some OAuth-based web services need to access other web service APIs that accept SAML assertions in non-interactive flows. Azure Active Directory can provide a SAML assertion in response to an On-Behalf-Of flow that uses a SAML-based web service as a target resource.

>[!NOTE]
>This is a non-standard extension to the OAuth 2.0 On-Behalf-Of flow that allows an OAuth2-based application to access web service API endpoints that consume SAML tokens.

> [!TIP]
> When you call a SAML-protected web service from a front-end web application, you can simply call the API and initiate a normal interactive authentication flow with the user's existing session. You only need to use an OBO flow when a service-to-service call requires a SAML token to provide user context.

### Obtain a SAML token by using an OBO request with a shared secret

A service-to-service request for a SAML assertion contains the following parameters:

| Parameter | Type | Description |
| --- | --- | --- |
| grant_type |required | The type of the token request. For a request that uses a JWT, the value must be **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |required | The value of the access token used in the request.|
| client_id |required | The app ID assigned to the calling service during registration with Azure AD. To find the app ID in the Azure portal, select **Active Directory**, choose the directory, and then select the application name. |
| client_secret |required | The key registered for the calling service in Azure AD. This value should have been noted at the time of registration. |
| resource |required | The app ID URI of the receiving service (secured resource). This is the resource that will be the Audience of the SAML token. To find the app ID URI in the Azure portal, select **Active Directory** and choose the directory. Select the application name, choose **All settings**, and then select **Properties**. |
| requested_token_use |required | Specifies how the request should be processed. In the On-Behalf-Of flow, the value must be **on_behalf_of**. |
| requested_token_type | required | Specifies the type of token requested. The value can be **urn:ietf:params:oauth:token-type:saml2** or **urn:ietf:params:oauth:token-type:saml1** depending on the requirements of the accessed resource. |

The response contains a SAML token encoded in UTF8 and Base64url.

- **SubjectConfirmationData for a SAML assertion sourced from an OBO call**: If the target application requires a recipient value in **SubjectConfirmationData**, then the value must be a non-wildcard Reply URL in the resource application configuration.
- **The SubjectConfirmationData node**: The node can't contain an **InResponseTo** attribute since it's not part of a SAML response. The application receiving the SAML token must be able to accept the SAML assertion without an **InResponseTo** attribute.

- **Consent**: Consent must have been granted to receive a SAML token containing user data on an OAuth flow. For information on permissions and obtaining administrator consent, see [Permissions and consent in the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent).

### Response with SAML assertion

| Parameter | Description |
| --- | --- |
| token_type |Indicates the token type value. The only type that Azure AD supports is **Bearer**. For more information about bearer tokens, see [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |The scope of access granted in the token. |
| expires_in |The length of time the access token is valid (in seconds). |
| expires_on |The time when the access token expires. The date is represented as the number of seconds from 1970-01-01T0:0:0Z UTC until the expiration time. This value is used to determine the lifetime of cached tokens. |
| resource |The app ID URI of the receiving service (secured resource). |
| access_token |The parameter that returns the SAML assertion. |
| refresh_token |The refresh token. The calling service can use this token to request another access token after the current SAML assertion expires. |

- token_type: Bearer
- expires_in: 3296
- ext_expires_in: 0
- expires_on: 1529627844
- resource: `https://api.contoso.com`
- access_token: \<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token: \<Refresh token\>


## Gaining consent for the middle-tier application

Depending on the architecture or usage of your application, you may consider different strategies for ensuring that the OBO flow is successful. In all cases, the ultimate goal is to ensure proper consent is given so that the client app can call the middle-tier app, and the middle tier app has permission to call the back-end resource.

> [!NOTE]
> Previously the Microsoft account system (personal accounts) did not support the "Known client application" field, nor could it show combined consent.  This has been added and all apps in the Microsoft identity platform can use the known client application approach for getting consent for OBO calls.

### /.default and combined consent

The middle tier application adds the client to the known client applications list in its manifest, and then the client can trigger a combined consent flow for both itself and the middle tier application. On the Microsoft identity platform endpoint, this is done using the [`/.default` scope](v2-permissions-and-consent.md#the-default-scope). When triggering a consent screen using known client applications and `/.default`, the consent screen will show permissions for **both** the client to the middle tier API, and also request whatever permissions are required by the middle-tier API. The user provides consent for both applications, and then the OBO flow works.

### Pre-authorized applications

Resources can indicate that a given application always has permission to receive certain scopes. This is primarily useful to make connections between a front-end client and a back-end resource more seamless. A resource can declare multiple pre-authorized applications - any such application can request these permissions in an OBO flow and receive them without the user providing consent.

### Admin consent

A tenant admin can guarantee that applications have permission to call their required APIs by providing admin consent for the middle tier application. To do this, the admin can find the middle tier application in their tenant, open the required permissions page, and choose to give permission for the app. To learn more about admin consent, see the [consent and permissions documentation](v2-permissions-and-consent.md).

### Use of a single application

In some scenarios, you may only have a single pairing of middle-tier and front-end client. In this scenario, you may find it easier to make this a single application, negating the need for a middle-tier application altogether. To authenticate between the front-end and the web API, you can use cookies, an id_token, or an access token requested for the application itself. Then, request consent from this single application to the back-end resource.

## Client limitations

If a client uses the implicit flow to get an id_token, and that client also has wildcards in a reply URL, the id_token can't be used for an OBO flow.  However, access tokens acquired through the implicit grant flow can still be redeemed by a confidential client even if the initiating client has a wildcard reply URL registered.

## Next steps

Learn more about the OAuth 2.0 protocol and another way to perform service to service auth using client credentials.

* [OAuth 2.0 client credentials grant in Microsoft identity platform](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 code flow in Microsoft identity platform](v2-oauth2-auth-code-flow.md)
* [Using the `/.default` scope](v2-permissions-and-consent.md#the-default-scope)
