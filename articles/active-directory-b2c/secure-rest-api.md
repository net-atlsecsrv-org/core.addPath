---
title: Secure a Restful service in your Azure AD B2C
titleSuffix: Azure AD B2C
description: Secure your custom REST API claims exchanges in your Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
---

# Secure your API Connector 


When integrating a REST API within an Azure AD B2C user flow, you must protect your REST API endpoint with authentication. The REST API authentication ensures that only services that have proper credentials, such as Azure AD B2C, can make calls to your endpoint. This article will explore how to secure REST API. 

## Prerequisites

Complete the steps in the [Walkthrough: Add an API connector to a sign-up user flow](add-api-connector.md) guide.

## HTTP basic authentication

HTTP basic authentication is defined in [RFC 2617](https://tools.ietf.org/html/rfc2617). Basic authentication works as follows: Azure AD B2C sends an HTTP request with the client credentials in the Authorization header. The credentials are formatted as the base64-encoded string "name:password".  

::: zone pivot="b2c-user-flow"

To configure an API Connector with HTTP basic authentication, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **Azure AD B2C**.
1. Select **API connectors (Preview)**, and then select the **API Connector** you want to configure.
1. For the **Authentication type**, select **Basic**.
1. Provide the **Username**, and **Password** of your REST API endpoint.
1. Select **Save**.

::: zone-end

::: zone pivot="b2c-custom-policy"

### Add REST API username and password policy keys

To configure a REST API technical profile with HTTP basic authentication, create the following cryptographic keys to store the username and password:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose your Azure AD B2C directory.
1. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
1. On the Overview page, select **Identity Experience Framework**.
1. Select **Policy Keys**, and then select **Add**.
1. For **Options**, select **Manual**.
1. For **Name**, type **RestApiUsername**.
    The prefix *B2C_1A_* might be added automatically.
1. In the **Secret** box, enter the REST API username.
1. For **Key usage**, select **Encryption**.
1. Select **Create**.
1. Select **Policy Keys** again.
1. Select **Add**.
1. For **Options**, select **Manual**.
1. For **Name**, type **RestApiPassword**.
    The prefix *B2C_1A_* might be added automatically.
1. In the **Secret** box, enter the REST API password.
1. For **Key usage**, select **Encryption**.
1. Select **Create**.

### Configure your REST API technical profile to use HTTP basic authentication

After creating the necessary keys, configure your REST API technical profile metadata to reference the credentials.

1. In your working directory, open the extension policy file (TrustFrameworkExtensions.xml).
1. Search for the REST API technical profile. For example `REST-ValidateProfile`, or `REST-GetProfile`.
1. Locate the `<Metadata>` element.
1. Change the *AuthenticationType* to `Basic`.
1. Change the *AllowInsecureAuthInProduction* to `false`.
1. Immediately after the closing `</Metadata>` element, add the following XML snippet:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

The following XML snippet is an example of a RESTful technical profile configured with HTTP basic authentication:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## HTTPS client certificate authentication

Client certificate authentication is a mutual certificate-based authentication, where the client, Azure AD B2C, provides its client certificate to the server to prove its identity. This happens as a part of the SSL handshake. Only services that have proper certificates, such as Azure AD B2C, can access your REST API service. The client certificate is an X.509 digital certificate. In production environments, it must be signed by a certificate authority.

### Prepare a self-signed certificate (optional)

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### Configure your API Connector

To configure an API Connector with client certificate authentication, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Under **Azure services**, select **Azure AD B2C**.
1. Select **API connectors (Preview)**, and then select the **API Connector** you want to configure.
1. For the **Authentication type**, select **Certificate**.
1. In the **Upload certificate** box, select your certificate's .pfx file with a private key.
1. In the **Enter Password** box, type the certificate's password.
1. Select **Save**.

::: zone-end

::: zone pivot="b2c-custom-policy"

### Add a client certificate policy key

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose your Azure AD B2C directory.
1. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
1. On the Overview page, select **Identity Experience Framework**.
1. Select **Policy Keys**, and then select **Add**.
1. In the **Options** box, select **Upload**.
1. In the **Name** box, type **RestApiClientCertificate**.
    The prefix *B2C_1A_* is added automatically.
1. In the **File upload** box, select your certificate's .pfx file with a private key.
1. In the **Password** box, type the certificate's password.
1. Select **Create**.

### Configure your REST API technical profile to use client certificate authentication

After creating the necessary key, configure your REST API technical profile metadata to reference the client certificate.

1. In your working directory, open the extension policy file (TrustFrameworkExtensions.xml).
1. Search for the REST API technical profile. For example `REST-ValidateProfile`, or `REST-GetProfile`.
1. Locate the `<Metadata>` element.
1. Change the *AuthenticationType* to `ClientCertificate`.
1. Change the *AllowInsecureAuthInProduction* to `false`.
1. Immediately after the closing `</Metadata>` element, add the following XML snippet:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

The following XML snippet is an example of a RESTful technical profile configured with an HTTP client certificate:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## OAuth2 bearer authentication 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Bearer token authentication is defined in [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). In bearer token authentication, Azure AD B2C sends an HTTP request with a token in the authorization header.

```http
Authorization: Bearer <token>
```

A bearer token is an opaque string. It can be a JWT access token or any string that the REST API expects Azure AD B2C to send in the authorization header. Azure AD B2C supports the following types:

- **Bearer token**. To be able to send the bearer token in the Restful technical profile, your policy needs to first acquire the bearer token and then use it in the RESTful technical profile.  
- **Static bearer token**. Use this approach when your REST API issues a long-term access token. To use a static bearer token, create a policy key and make a reference from the RESTful technical profile to your policy key. 


## Using OAuth2 Bearer  

The following steps demonstrate how to use client credentials to obtain a bearer token and pass it into the Authorization header of the REST API calls.  

### Define a claim to store the bearer token

A claim provides temporary storage of data during an Azure AD B2C policy execution. The [claims schema](claimsschema.md) is the place where you declare your claims. The access token must be stored in a claim to be used later. 

1. Open the extensions file of your policy. For example, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Search for the [BuildingBlocks](buildingblocks.md) element. If the element doesn't exist, add it.
1. Locate the [ClaimsSchema](claimsschema.md) element. If the element doesn't exist, add it.
1. Add the following claims to the **ClaimsSchema** element.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### Acquiring an access token 

You can obtain an access token in one of several ways: by obtaining it [from a federated identity provider](idp-pass-through-user-flow.md), by calling a REST API that returns an access token, by using an [ROPC flow](../active-directory/develop/v2-oauth-ropc.md), or by using the [client credentials flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). The client credentials flow is commonly used for server-to-server interactions that must run in the background, without immediate interaction with a user.

#### Acquiring an Azure AD access token 

The following example uses a REST API technical profile to make a request to the Azure AD token endpoint using the client credentials passed as HTTP basic authentication. For more information, see [Microsoft identity platform and the OAuth 2.0 client credentials flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Before the technical profile can interact with Azure AD to obtain an access token, you need to register an application. Azure AD B2C relies the Azure AD platform. You can create the app in your Azure AD B2C tenant, or in any Azure AD tenant you manage. To register an application:

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select the **Directory + subscription** filter in the top menu, and then select the directory that contains your Azure AD, or Azure AD B2C tenant.
1. In the left menu, select **Azure Active Directory**. Or, select **All services** and search for and select **Azure Active Directory**.
1. Select **App registrations**, and then select **New registration**.
1. Enter a **Name** for the application. For example, *Client_Credentials_Auth_app*.
1. Under **Supported account types**, select **Accounts in this organizational directory only**.
1. Select **Register**.
2. Record the **Application (client) ID**. 


For a client credentials flow, you need to create an application secret. The client secret is also known as an application password. The secret will be used by your application to acquire an access token.

1. In the **Azure AD - App registrations** page, select the application you created, for example *Client_Credentials_Auth_app*.
1. In the left menu, under **Manage**, select **Certificates & secrets**.
1. Select **New client secret**.
1. Enter a description for the client secret in the **Description** box. For example, *clientsecret1*.
1. Under **Expires**, select a duration for which the secret is valid, and then select **Add**.
1. Record the secret's **Value** for use in your client application code. This secret value is never displayed again after you leave this page. You use this value as the application secret in your application's code.

#### Create Azure AD B2C policy keys

You need to store the client ID and the client secret that you previously recorded in your Azure AD B2C tenant.

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose the directory that contains your tenant.
3. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
4. On the Overview page, select **Identity Experience Framework**.
5. Select **Policy Keys** and then select **Add**.
6. For **Options**, choose `Manual`.
7. Enter a **Name** for the policy key, `SecureRESTClientId`. The prefix `B2C_1A_` is added automatically to the name of your key.
8. In **Secret**, enter your client ID that you previously recorded.
9. For **Key usage**, select `Signature`.
10. Select **Create**.
11. Create another policy key with the following settings:
    -   **Name**: `SecureRESTClientSecret`.
    -   **Secret**: enter your client secret that you previously recorded

For the ServiceUrl, replace your-tenant-name with the name of your Azure AD tenant. See the [RESTful technical profile](restful-technical-profile.md) reference for all options available.

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### Change the REST technical profile to use bearer token authentication

To support bearer token authentication in your custom policy, modify the REST API technical profile with the following:

1. In your working directory, open the *TrustFrameworkExtensions.xml* extension policy file.
1. Search for the `<TechnicalProfile>` node that includes `Id="REST-API-SignUp"`.
1. Locate the `<Metadata>` element.
1. Change the *AuthenticationType* to *Bearer*, as follows:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Change or add the *UseClaimAsBearerToken* to *bearerToken*, as follows. The *bearerToken* is the name of the claim that the bearer token will be retrieved from (the output claim from `REST-AcquireAccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Ensure you add the claim used above as an input claim:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

After you add the above snippets, your technical profile should look like the following XML code:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## Using a static OAuth2 bearer 

### Add the OAuth2 bearer token policy key

To configure a REST API technical profile with an OAuth2 bearer token, obtain an access token from the REST API owner. Then create the following cryptographic key to store the bearer token.

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose your Azure AD B2C directory.
1. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
1. On the Overview page, select **Identity Experience Framework**.
1. Select **Policy Keys**, and then select **Add**.
1. For **Options**, choose `Manual`.
1. Enter a **Name** for the policy key. For example, `RestApiBearerToken`. The prefix `B2C_1A_` is added automatically to the name of your key.
1. In **Secret**, enter your client secret that you previously recorded.
1. For **Key usage**, select `Encryption`.
1. Select **Create**.

### Configure your REST API technical profile to use the bearer token policy key

After creating the necessary key, configure your REST API technical profile metadata to reference the bearer token.

1. In your working directory, open the extension policy file (TrustFrameworkExtensions.xml).
1. Search for the REST API technical profile. For example `REST-ValidateProfile`, or `REST-GetProfile`.
1. Locate the `<Metadata>` element.
1. Change the *AuthenticationType* to `Bearer`.
1. Change the *AllowInsecureAuthInProduction* to `false`.
1. Immediately after the closing `</Metadata>` element, add the following XML snippet:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

The following XML snippet is an example of a RESTful technical profile configured with bearer token authentication:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## API key authentication

API key is a unique identifier used to authenticate a user to access a REST API endpoint. The key is sent in a custom HTTP header. For example, the [Azure Functions HTTP trigger](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) uses the `x-functions-key` HTTP header to identify the requester.  

### Add API key policy keys

To configure a REST API technical profile with API key authentication, create the following cryptographic key to store the API key:

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose your Azure AD B2C directory.
1. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
1. On the Overview page, select **Identity Experience Framework**.
1. Select **Policy Keys**, and then select **Add**.
1. For **Options**, select **Manual**.
1. For **Name**, type **RestApiKey**.
    The prefix *B2C_1A_* might be added automatically.
1. In the **Secret** box, enter the REST API key.
1. For **Key usage**, select **Encryption**.
1. Select **Create**.


### Configure your REST API technical profile to use API key authentication

After creating the necessary key, configure your REST API technical profile metadata to reference the credentials.

1. In your working directory, open the extension policy file (TrustFrameworkExtensions.xml).
1. Search for the REST API technical profile. For example `REST-ValidateProfile`, or `REST-GetProfile`.
1. Locate the `<Metadata>` element.
1. Change the *AuthenticationType* to `ApiKeyHeader`.
1. Change the *AllowInsecureAuthInProduction* to `false`.
1. Immediately after the closing `</Metadata>` element, add the following XML snippet:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

The **Id** of the cryptographic key defines the HTTP header. In this example, the API key is sent as **x-functions-key**.

The following XML snippet is an example of a RESTful technical profile configured to call an Azure Function with API key authentication:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## Next steps

- Learn more about the [Restful technical profile](restful-technical-profile.md) element in the custom policy reference.

::: zone-end