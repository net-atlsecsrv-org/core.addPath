---
title: Manage SSO and token customization using custom policies
titleSuffix: Azure AD B2C
description: Learn about managing SSO and token customization using custom policies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
---

# Manage SSO and token customization using custom policies in Azure Active Directory B2C

This article provides information about how you can manage your token, session, and single sign-on (SSO) configurations using [custom policies](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## JWT token lifetimes and claims configuration

To change the settings on your token lifetimes, you add a [ClaimsProviders](claimsproviders.md) element in the relying party file of the policy you want to impact.  The **ClaimsProviders** element is a child of the [TrustFrameworkPolicy](trustframeworkpolicy.md) element.

Insert the ClaimsProviders element between the BasePolicy element and the RelyingParty element of the relying party file.

Inside, you'll need to put the information that affects your token lifetimes. The XML looks like this example:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

The following values are set in the previous example:

- **Access token lifetimes** - The access token lifetime value is set with **token_lifetime_secs** metadata item. The default value is 3600 seconds (60 minutes).
- **ID token lifetime** - The ID token lifetime value is set with the **id_token_lifetime_secs** metadata item. The default value is 3600 seconds (60 minutes).
- **Refresh token lifetime** - The refresh token lifetime value is set with the **refresh_token_lifetime_secs** metadata item. The default value is 1209600 seconds (14 days).
- **Refresh token sliding window lifetime** - If you would like to set a sliding window lifetime to your refresh token, set the value of **rolling_refresh_token_lifetime_secs** metadata item. The default value is 7776000 (90 days). If you don't want to enforce a sliding window lifetime, replace the item with `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Issuer (iss) claim** - The Issuer (iss) claim is set with the **IssuanceClaimPattern** metadata item. The applicable values are `AuthorityAndTenantGuid` and `AuthorityWithTfp`.
- **Setting claim representing policy ID** - The options for setting this value are `TFP` (trust framework policy) and `ACR` (authentication context reference). `TFP` is the recommended value. Set **AuthenticationContextReferenceClaimPattern** with the value of `None`.

    In the **ClaimsSchema** element, add this element:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    In your **OutputClaims** element, add this element:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    For ACR, remove the **AuthenticationContextReferenceClaimPattern** item.

- **Subject (sub) claim** - This option defaults to ObjectID, if you would like to switch this setting to `Not Supported`, replace this line:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    with this line:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Single-page applications using the authorization code flow with PKCE always have a refresh token lifetime of 24 hours. [Learn more about the security implications of refresh tokens in the browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## Provide optional claims to your app

The [Relying party policy technical profile](relyingparty.md#technicalprofile) output claims are values that are returned to an application. Adding output claims will issue the claims into the token after a successful user journey, and will be sent to the application. Modify the technical profile element within the relying party section to add the desired claims as an output claim.

1. Open your custom policy file. For example, SignUpOrSignin.xml.
1. Find the OutputClaims element. Add the OutputClaim you want to be included in the token. 
1. Set the output claim attributes. 

The following example adds the `accountBalance` claim. The accountBalance claim is sent to the application as a balance. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

The OutputClaim element contains the following attributes:

  - **ClaimTypeReferenceId** - The identifier of a claim type already defined in the [ClaimsSchema](claimsschema.md) section in the policy file or parent policy file.
  - **PartnerClaimType** - Allows you to change the name of the claim in the token. 
  - **DefaultValue** - A default value. You can also set the default value to a [claim resolver](claim-resolver-overview.md), such as tenant ID.
  - **AlwaysUseDefaultValue** - Force the use of the default value.

## Next steps

- Learn more about [Azure AD B2C session](session-overview.md).
- Learn how to [configure session behavior in custom policies](session-behavior-custom-policy.md).
- Reference: [JwtIssuer](jwt-issuer-technical-profile.md).
