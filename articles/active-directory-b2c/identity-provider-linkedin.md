---
title: Set up sign-up and sign-in with a LinkedIn account
titleSuffix: Azure AD B2C
description: Provide sign-up and sign-in to customers with LinkedIn accounts in your applications using Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
---

# Set up sign-up and sign-in with a LinkedIn account using Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## Prerequisites

::: zone pivot="b2c-user-flow"

* [Create a user flow](tutorial-create-user-flows.md) to enable users to sign up and sign in to your application.
* If you haven't already done so, [add a web API application to your Azure Active Directory B2C tenant](add-web-api-application.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

* Complete the steps in the [Get started with custom policies in Active Directory B2C](custom-policy-get-started.md).
* If you haven't already done so, [add a web API application to your Azure Active Directory B2C tenant](add-web-api-application.md).

::: zone-end

## Create a LinkedIn application

To use a LinkedIn account as an [identity provider](authorization-code-flow.md) in Azure Active Directory B2C (Azure AD B2C), you need to create an application in your tenant that represents it. If you don't already have a LinkedIn account, you can sign up at [https://www.linkedin.com/](https://www.linkedin.com/).

1. Sign in to the [LinkedIn Developers website](https://www.developer.linkedin.com/) with your LinkedIn account credentials.
1. Select **My Apps**, and then click **Create app**.
1. Enter **App name**, **LinkedIn Page**, **Privacy policy URL**, and **App logo**.
1. Agree to the LinkedIn **API Terms of Use** and click **Create app**.
1. Select the **Auth** tab. Under **Authentication Keys**, copy the values for **Client ID** and **Client Secret**. You'll need both of them to configure LinkedIn as an identity provider in your tenant. **Client Secret** is an important security credential.
1. Select the edit pencil next to **Authorized redirect URLs for your app**, and then select **Add redirect URL**. Enter `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`, replacing `your-tenant-name` with the name of your tenant. You need to use all lowercase letters when entering your tenant name even if the tenant is defined with uppercase letters in Azure AD B2C. Select **Update**.
2. By default, your LinkedIn app isn't approved for scopes related to sign in. To request a review, select the **Products** tab, and then select **Sign In with LinkedIn**. When the review is complete, the required scopes will be added to your application.
   > [!NOTE]
   > You can view the scopes that are currently allowed for your app on the **Auth** tab in the **OAuth 2.0 scopes** section.

::: zone pivot="b2c-user-flow"

## Configure a LinkedIn account as an identity provider

1. Sign in to the [Azure portal](https://portal.azure.com/) as the global administrator of your Azure AD B2C tenant.
1. Make sure you're using the directory that contains your Azure AD B2C tenant by selecting the **Directory + subscription** filter in the top menu and choosing the directory that contains your tenant.
1. Choose **All services** in the top-left corner of the Azure portal, search for and select **Azure AD B2C**.
1. Select **Identity providers**, then select **LinkedIn**.
1. Enter a **Name**. For example, *LinkedIn*.
1. For the **Client ID**, enter the Client ID of the LinkedIn application that you created earlier.
1. For the **Client secret**, enter the Client Secret that you recorded.
1. Select **Save**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## Create a policy key

You need to store the client secret that you previously recorded in your Azure AD B2C tenant.

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose the directory that contains your tenant.
3. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
4. On the Overview page, select **Identity Experience Framework**.
5. Select **Policy keys** and then select **Add**.
6. For **Options**, choose `Manual`.
7. Enter a **Name** for the policy key. For example, `LinkedInSecret`. The prefix *B2C_1A_* is added automatically to the name of your key.
8. In **Secret**, enter the client secret that you previously recorded.
9. For **Key usage**, select `Signature`.
10. Click **Create**.

## Add a claims provider

If you want users to sign in using a LinkedIn account, you need to define the account as a claims provider that Azure AD B2C can communicate with through an endpoint. The endpoint provides a set of claims that are used by Azure AD B2C to verify that a specific user has authenticated.

Define a LinkedIn account as a claims provider by adding it to the **ClaimsProviders** element in the extension file of your policy.

1. Open the *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** file in your editor. This file is in the [Custom policy starter pack][starter-pack] you downloaded as part of one of the prerequisites.
1. Find the **ClaimsProviders** element. If it does not exist, add it under the root element.
1. Add a new **ClaimsProvider** as follows:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Replace the value of **client_id** with the client ID of the LinkedIn application that you previously recorded.
1. Save the file.

### Add the claims transformations

The LinkedIn technical profile requires the **ExtractGivenNameFromLinkedInResponse** and **ExtractSurNameFromLinkedInResponse** claims transformations to be added to the list of ClaimsTransformations. If you don't have a **ClaimsTransformations** element defined in your file, add the parent XML elements as shown below. The claims transformations also need a new claim type defined named **nullStringClaim**.

Add the **BuildingBlocks** element near the top of the *TrustFrameworkExtensions.xml* file. See *TrustFrameworkBase.xml* for an example.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### Upload the extension file for verification

You now have a policy configured so that Azure AD B2C knows how to communicate with your LinkedIn account. Try uploading the extension file of your policy to confirm that it doesn't have any issues so far.

1. On the **Custom Policies** page in your Azure AD B2C tenant, select **Upload Policy**.
1. Enable **Overwrite the policy if it exists**, and then browse to and select the *TrustFrameworkExtensions.xml* file.
1. Click **Upload**.

## Register the claims provider

At this point, the identity provider has been set up, but it's not available in any of the sign-up or sign-in screens. To make it available, you create a duplicate of an existing template user journey, and then modify it so that it also has the LinkedIn identity provider.

1. Open the *TrustFrameworkBase.xml* file in the starter pack.
1. Find and copy the entire contents of the **UserJourney** element that includes `Id="SignUpOrSignIn"`.
1. Open the *TrustFrameworkExtensions.xml* and find the **UserJourneys** element. If the element doesn't exist, add one.
1. Paste the entire content of the **UserJourney** element that you copied as a child of the **UserJourneys** element.
1. Rename the ID of the user journey. For example, `SignUpSignInLinkedIn`.

### Display the button

The **ClaimsProviderSelection** element is analogous to an identity provider button on a sign-up or sign-in screen. If you add a **ClaimsProviderSelection** element for a LinkedIn account, a new button shows up when a user lands on the page.

1. Find the **OrchestrationStep** element that includes `Order="1"` in the user journey that you created.
2. Under **ClaimsProviderSelections**, add the following element. Set the value of **TargetClaimsExchangeId** to an appropriate value, for example `LinkedInExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### Link the button to an action

Now that you have a button in place, you need to link it to an action. The action, in this case, is for Azure AD B2C to communicate with a LinkedIn account to receive a token.

1. Find the **OrchestrationStep** that includes `Order="2"` in the user journey.
1. Add the following **ClaimsExchange** element making sure that you use the same value for the ID that you used for **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Update the value of **TechnicalProfileReferenceId** to the ID of the technical profile you created earlier. For example, `LinkedIn-OAUTH`.

1. Save the *TrustFrameworkExtensions.xml* file and upload it again for verification.

::: zone-end

:: zone pivot="b2c-user-flow"

## Add LinkedIn identity provider to a user flow 

1. In your Azure AD B2C tenant, select **User flows**.
1. Click the user flow that you want to the LinkedIn identity provider.
1. Under the **Social identity providers**, select **LinkedIn**.
1. Select **Save**.
1. To test your policy, select **Run user flow**.
1. For **Application**, select the web application named *testapp1* that you previously registered. The **Reply URL** should show `https://jwt.ms`.
1. Click **Run user flow**

::: zone-end

::: zone pivot="b2c-custom-policy"

## Update and test the relying party file

Update the relying party (RP) file that initiates the user journey that you created.

1. Make a copy of *SignUpOrSignIn.xml* in your working directory, and rename it. For example, rename it to *SignUpSignInLinkedIn.xml*.
1. Open the new file and update the value of the **PolicyId** attribute for **TrustFrameworkPolicy** with a unique value. For example, `SignUpSignInLinkedIn`.
1. Update the value of **PublicPolicyUri** with the URI for the policy. For example,`http://contoso.com/B2C_1A_signup_signin_linkedin`
1. Update the value of the **ReferenceId** attribute in **DefaultUserJourney** to match the ID of the new user journey that you created (SignUpSignLinkedIn).
1. Save your changes, upload the file, and then select the new policy in the list.
1. Make sure that Azure AD B2C application that you created is selected in the **Select application** field, and then test it by clicking **Run now**.


## Migration from v1.0 to v2.0

LinkedIn recently [updated their APIs from v1.0 to v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). To migrate your existing configuration to the new configuration, use the information in the following sections to update the elements in the technical profile.

### Replace items in the Metadata

In the existing **Metadata** element of the **TechnicalProfile**, update the following **Item** elements from:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

To:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### Add items to the Metadata

In the **Metadata** of the **TechnicalProfile**, add the following **Item** elements:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### Update the OutputClaims

In the existing **OutputClaims** of the **TechnicalProfile**, update the following **OutputClaim** elements from:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

To:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### Add new OutputClaimsTransformation elements

In the **OutputClaimsTransformations** of the **TechnicalProfile**, add the following **OutputClaimsTransformation** elements:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### Define the new claims transformations and claim type

In the last step, you added new claims transformations that need to be defined. To define the claims transformations, add them to the list of **ClaimsTransformations**. If you don't have a **ClaimsTransformations** element defined in your file, add the parent XML elements as shown below. The claims transformations also need a new claim type defined named **nullStringClaim**.

The **BuildingBlocks** element should be added near the top of the file. See the *TrustframeworkBase.xml* as an example.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### Obtain an email address

As part of the LinkedIn migration from v1.0 to v2.0, an additional call to another API is required to obtain the email address. If you need to obtain the email address during sign-up, do the following:

1. Complete the steps above to allow Azure AD B2C to federate with LinkedIn to let the user sign in. As part of the federation, Azure AD B2C receives the access token for LinkedIn.
1. Save the LinkedIn access token into a claim. [See the instructions here](idp-pass-through-custom.md).
1. Add the following claims provider that makes the request to LinkedIn's `/emailAddress` API. In order to authorize this request, you need the LinkedIn access token.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Add the following orchestration step into your user journey, so that the API claims provider is triggered when a user signs in using LinkedIn. Make sure to update the `Order` number appropriately. Add this step immediately after the orchestration step that triggers the LinkedIn technical profile.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Obtaining the email address from LinkedIn during sign-up is optional. If you choose not to obtain the email from LinkedIn but require one during sign up, the user is required to manually enter the email address and validate it.

For a full sample of a policy that uses the LinkedIn identity provider, see the [Custom Policy Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## Migration from v1.0 to v2.0

LinkedIn recently [updated their APIs from v1.0 to v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). As part of the migration, Azure AD B2C is only able to obtain the full name of the LinkedIn user during the sign-up. If an email address is one of the attributes that is collected during sign-up, the user must manually enter the email address and validate it.

::: zone-end