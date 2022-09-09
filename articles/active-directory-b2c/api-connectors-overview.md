---
title: About API connectors in Azure AD B2C
description: Use Azure Active Directory (Azure AD) API connectors to customize and extend your user flows by using REST APIs. 
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 04/30/2021

ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: "it-pro"
zone_pivot_groups: b2c-policy-type
---

# Use API connectors to customize and extend sign-up user flows

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

> [!IMPORTANT]
> API connectors for sign-up is a public preview feature of Azure AD B2C. For more information about previews, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## Overview 

As a developer or IT administrator, you can use API connectors to integrate your sign-up user flows with REST APIs to customize the sign-up experience and integrate with external systems. For example, with API connectors, you can:

- **Validate user input data**. Validate against malformed or invalid user data. For example, you can validate user-provided data against existing data in an external data store or list of permitted values. If invalid, you can ask a user to provide valid data or block the user from continuing the sign-up flow.
- **Integrate with a custom approval workflow**. Connect to a custom approval system for managing and limiting account creation.
- **Overwrite user attributes**. Reformat or assign a value to an attribute collected from the user. For example, if a user enters the first name in all lowercase or all uppercase letters, you can format the name with only the first letter capitalized. 
- **Verify user identity**. Use an identity verification service to add an extra level of security to account creation decisions.
- **Run custom business logic**. You can trigger downstream events in your cloud systems to send push notifications, update corporate databases, manage permissions, audit databases, and perform other custom actions.

An API connector provides Azure AD B2C with the information needed to call API endpoint by defining the HTTP endpoint URL and authentication for the API call. Once you configure an API connector, you can enable it for a specific step in a user flow. When a user reaches that step in the sign up flow, the API connector is invoked and materializes as an HTTP POST request to your API, sending user information ("claims") as key-value pairs in a JSON body. The API response can affect the execution of the user flow. For example, the API response can block a user from signing up, ask the user to reenter information, or overwrite and append user attributes.

## Where you can enable an API connector in a user flow

There are two places in a user flow where you can enable an API connector:

- After signing in with an identity provider
- Before creating the user

> [!IMPORTANT]
> In both of these cases, the API connectors are invoked during user **sign-up**, not sign-in.

### After signing in with an identity provider

An API connector at this step in the sign-up process is invoked immediately after the user authenticates with an identity provider (like Google, Facebook, & Azure AD). This step precedes the ***attribute collection page***, which is the form presented to the user to collect user attributes. This step is not invoked if a user is registering with a local account. The following are examples of API connector scenarios you might enable at this step:

- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Implement an allow or blocklist based on social identity.

### Before creating the user

An API connector at this step in the sign-up process is invoked after the attribute collection page, if one is included. This step is always invoked before a user account is created. The following are examples of scenarios you might enable at this point during sign-up:

- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Verify user identity.
- Query external systems for existing data about the user to return it in the application token or store it in Azure AD.

::: zone-end

::: zone pivot="b2c-custom-policy"

The Identity Experience Framework, which underlies Azure Active Directory B2C (Azure AD B2C), can integrate with RESTful APIs within a user journey. This article shows how to create a user journey that interacts with a RESTful service using a [RESTful technical profile](restful-technical-profile.md).

Using Azure AD B2C, you can add your own business logic to a user journey by calling your own RESTful service. The Identity Experience Framework can send and receive data from your RESTful service to exchange claims. For example, you can:

- **Validate user input data**. For example, you can verify that the email address provided by the user exists in your customer's database, and if not, present an error.
- **Process claims**. If a user enters their first name in all lowercase or all uppercase letters, your REST API can format the name with only the first letter capitalized and return it to Azure AD B2C.
- **Enrich user data by further integrating with corporate line-of-business applications**. Your RESTful service can receive the user's email address, query the customer's database, and return the user's loyalty number to Azure AD B2C. Then return claims can be stored in the user's Azure AD account, evaluated in the next orchestration steps, or included in the access token.
- **Run custom business logic**. You can send push notifications, update corporate databases, run a user migration process, manage permissions, audit databases, and perform any other workflows.

![Diagram of a RESTful service claims exchange](media/api-connectors-overview/restful-service-claims-exchange.png)

> [!NOTE]
> If there is slow or no response from the RESTful service to Azure AD B2C, the timeout is 30 seconds and the retry count is two times (meaning there are 3 tries in total). The timeout and retry count settings are not currently configurable.

## Calling a RESTful service

The interaction includes a claims exchange of information between the REST API claims and Azure AD B2C. You can design the integration with the RESTful services in the following ways:

- **Validation technical profile**. The call to the RESTful service happens within a [validation technical profile](validation-technical-profile.md) of the specified [self-asserted technical profile](self-asserted-technical-profile.md), or a [verification display control](display-control-verification.md) of a [display control](display-controls.md). The validation technical profile validates the user-provided data before the user journey moves forward. With the validation technical profile, you can:

  - Send claims to your REST API.
  - Validate claims, and throw custom error messages that are displayed to the user.
  - Send back claims from the REST API to next orchestration steps.

- **Claims exchange**. A direct claims exchange can be configured by calling a REST API technical profile directly from an orchestration step of a [user journey](userjourneys.md). This definition is limited to:

  - Send claims to your REST API.
  - Validate claims, and throw custom error messages that are returned to the application.
  - Send back claims from the REST API to next orchestration steps.

You can add a REST API call at any step in the user journey defined by a custom policy. For example, you can call a REST API:

- During sign-in, just before Azure AD B2C validates the credentials.
- Immediately after sign-in.
- Before Azure AD B2C creates a new account in the directory.
- After Azure AD B2C creates a new account in the directory.
- Before Azure AD B2C issues an access token.

![Validation technical profile collection](media/api-connectors-overview/validation-technical-profile.png)

## Sending data

In the [RESTful technical profile](restful-technical-profile.md), the `InputClaims` element contains a list of claims to send to your RESTful service. You can map the name of your claim to the name defined in the RESTful service, set a default value, and use [claims resolvers](claim-resolver-overview.md).

You can configure how the input claims are sent to the RESTful claims provider by using the SendClaimsIn attribute. The possible values are:

- **Body**, sent in the HTTP POST request body in JSON format.
- **Form**, sent in the HTTP POST request body in an ampersand '&' separated key value format.
- **Header**, sent in the HTTP GET request header.
- **QueryString**, sent in the HTTP GET request query string.

When the **Body** option is configured, the REST API technical profile allows you to send a complex JSON payload to an endpoint. For more information, see [Send a JSON payload](restful-technical-profile.md#send-a-json-payload).

## Receiving data

The `OutputClaims` element of the [RESTful technical profile](restful-technical-profile.md) contains a list of claims returned by the REST API. You may need to map the name of the claim defined in your policy to the name defined in the REST API. You can also include claims that aren't returned by the REST API identity provider, as long as you set the DefaultValue attribute.

The output claims parsed by the RESTful claims provider always expect to parse a flat JSON Body response, such as:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

The output claims should look like the following xml snippet:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

To parse a nested JSON Body response, set the ResolveJsonPathsInJsonTokens metadata to true. In the output claim, set the PartnerClaimType to the JSON path element you want to output.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


The output claims should look like following xml snippet:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## Localize the REST API

In a RESTful technical profile, you may want to send the current session's language/locale, and if necessary, raise a localized error message. Using the [claims resolver](claim-resolver-overview.md), you can send a contextual claim, such as the user language. The following example shows a RESTful technical profile demonstrating this scenario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## Handling error messages

Your REST API may need to return an error message, such as "The user was not found in the CRM system." If an error occurs, the REST API should return an HTTP 409 error message (Conflict response status code). For more information, see the [RESTful technical profile](restful-technical-profile.md#returning-validation-error-message).

This behavior can only be achieved by calling a REST API technical profile from a validation technical profile. Letting the user to correct the data on the page and run the validation again upon page submission.

If you reference a REST API technical profile directly from a user journey, the user is redirected back to the relying party application with the relevant error message.

::: zone-end

## Security considerations

You protect your REST API endpoint so that only authenticated clients can communicate with it. The REST API must use an HTTPS endpoint. Set the authentication type to one of the following authentication methods.

### API Key

API key is a unique identifier used to authenticate a user to access a REST API endpoint. For example, [Azure Functions HTTP trigger](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) includes the `code` as a query parameter in the endpoint URL.

::: zone pivot="b2c-user-flow"

```http
https://contoso.azurewebsites.net/api/endpoint?code=0123456789 
```

API key authentication shouldn't be used alone in production. Therefore, configuration for basic or certificate authentication is always required. If you do not wish to implement any authentication method (not recommended) for development purposes, you can choose basic authentication and use temporary values for `username` and `password` that your API can disregard while you implement the authorization in your API.

::: zone-end

::: zone pivot="b2c-custom-policy"

The API key can be sent a custom HTTP header. For example, the [Azure Functions HTTP trigger](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) uses the `x-functions-key` HTTP header to identify the requester.  

::: zone-end

### Client certificate

The client certificate authentication is a mutual certificate-based authentication method where the client provides a client certificate to the server to prove its identity. In this case, Azure AD B2C will use the certificate that you upload as part of the API connector configuration.  This behavior happens as a part of the SSL handshake. 

Your API service can then limit access to only services that have proper certificates. The client certificate is a PKCS12 (PFX) X.509 digital certificate. In production environments, it should be signed by a certificate authority.

### HTTP basic authentication

The HTTP basic authentication is defined in [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C sends an HTTP request with the client credentials (`username` and `password`) in the `Authorization` header. The credentials are formatted as the base64-encoded string `username:password`. Your API then checks these values to determine whether to reject an API call or not.

::: zone pivot="b2c-custom-policy"

### Bearer token

Bearer token authentication is defined in [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). In bearer token authentication, Azure AD B2C sends an HTTP request with a token in the authorization header.

```http
Authorization: Bearer <token>
```

A bearer token is an opaque string. It can be a JWT access token or any string that the REST API expects Azure AD B2C to send in the authorization header. 
 
::: zone-end

## REST API platform

Your REST API can be based on any platform and written in any programing language, as long as it's secure and can send and receive claims in JSON format.

The request to your REST API service comes from Azure AD B2C servers. The REST API service must be published to a publicly accessible HTTPS endpoint. The REST API calls will arrive from an Azure data center IP address.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

Design your REST API service and its underlying components (such as the database and file system) to be highly available.


## Next steps



::: zone pivot="b2c-user-flow"

- Learn how to [add an API connector to a user flow](add-api-connector.md)
- Get started with our [samples](code-samples.md#api-connectors).

::: zone-end

::: zone pivot="b2c-custom-policy"

See the following articles for examples of using a RESTful technical profile:

- [Walkthrough: Add an API connector to a sign-up user flow](add-api-connector.md)
- [Walkthrough: Add REST API claims exchanges to custom policies in Azure Active Directory B2C](custom-policy-rest-api-claims-exchange.md)
- [Secure your REST API services](secure-rest-api.md)
- [Reference: RESTful technical profile](restful-technical-profile.md)

::: zone-end