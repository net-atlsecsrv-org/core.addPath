---
title: Add AD FS as a SAML identity provider by using custom policies
titleSuffix: Azure AD B2C
description: Set up AD FS 2016 using the SAML protocol and custom policies in Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
---

# Add AD FS as a SAML identity provider using custom policies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

This article shows you how to enable sign-in for an AD FS user account by using [custom policies](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). You enable sign-in by adding a [SAML identity provider technical profile](saml-identity-provider-technical-profile.md) to a custom policy.

## Prerequisites

- Complete the steps in [Get started with custom policies in Azure Active Directory B2C](custom-policy-get-started.md).
- Make sure that you have access to a certificate .pfx file with a private key. You can generate your own signed certificate and upload it to Azure AD B2C. Azure AD B2C uses this certificate to sign the SAML request sent to your SAML identity provider. For more information about how to generate a certificate, see [Generate a signing certificate](identity-provider-salesforce-saml.md#generate-a-signing-certificate).
- In order for Azure to accept the .pfx file password, the password must be encrypted with the TripleDES-SHA1 option in Windows Certificate Store Export utility as opposed to AES256-SHA256.

## Create a policy key

You need to store your certificate in your Azure AD B2C tenant.

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Make sure you're using the directory that contains your Azure AD B2C tenant. Select the **Directory + subscription** filter in the top menu and choose the directory that contains your tenant.
3. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
4. On the Overview page, select **Identity Experience Framework**.
5. Select **Policy Keys** and then select **Add**.
6. For **Options**, choose `Upload`.
7. Enter a **Name** for the policy key. For example, `ADFSSamlCert`. The prefix `B2C_1A_` is added automatically to the name of your key.
8. Browse to and select your certificate .pfx file with the private key.
9. Click **Create**.

## Add a claims provider

If you want users to sign in using an AD FS account, you need to define the account as a claims provider that Azure AD B2C can communicate with through an endpoint. The endpoint provides a set of claims that are used by Azure AD B2C to verify that a specific user has authenticated.

You can define an AD FS account as a claims provider by adding it to the **ClaimsProviders** element in the extension file of your policy. For more information, see [define a SAML identity provider technical profile](saml-identity-provider-technical-profile.md).

1. Open the *TrustFrameworkExtensions.xml*.
1. Find the **ClaimsProviders** element. If it does not exist, add it under the root element.
1. Add a new **ClaimsProvider** as follows:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Replace `your-AD-FS-domain` with the name of your AD FS domain and replace the value of the **identityProvider** output claim with your DNS (Arbitrary value that indicates your domain).

1. Locate the `<ClaimsProviders>` section and add the following XML snippet. If your policy already contains the `SM-Saml-idp` technical profile, skip to the next step. For more information, see [single sign-on session management](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Save the file.

### Upload the extension file for verification

By now, you have configured your policy so that Azure AD B2C knows how to communicate with AD FS account. Try uploading the extension file of your policy just to confirm that it doesn't have any issues so far.

1. On the **Custom Policies** page in your Azure AD B2C tenant, select **Upload Policy**.
2. Enable **Overwrite the policy if it exists**, and then browse to and select the *TrustFrameworkExtensions.xml* file.
3. Click **Upload**.

> [!NOTE]
> The Visual Studio code B2C extension uses "socialIdpUserId." A social policy is also required for AD FS.
>

## Register the claims provider

At this point, the identity provider has been set up, but it’s not available in any of the sign-up or sign-in screens. To make it available, you create a duplicate of an existing template user journey, and then modify it so that it also has the AD FS identity provider.

1. Open the *TrustFrameworkBase.xml* file from the starter pack.
2. Find and copy the entire contents of the **UserJourney** element that includes `Id="SignUpOrSignIn"`.
3. Open the *TrustFrameworkExtensions.xml* and find the **UserJourneys** element. If the element doesn't exist, add one.
4. Paste the entire content of the **UserJourney** element that you copied as a child of the **UserJourneys** element.
5. Rename the ID of the user journey. For example, `SignUpSignInADFS`.

### Display the button

The **ClaimsProviderSelection** element is analogous to an identity provider button on a sign-up or sign-in screen. If you add a **ClaimsProviderSelection** element for an AD FS account, a new button shows up when a user lands on the page.

1. Find the **OrchestrationStep** element that includes `Order="1"` in the user journey that you created.
2. Under **ClaimsProviderSelections**, add the following element. Set the value of **TargetClaimsExchangeId** to an appropriate value, for example `ContosoExchange`:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### Link the button to an action

Now that you have a button in place, you need to link it to an action. The action, in this case, is for Azure AD B2C to communicate with an AD FS account to receive a token.

1. Find the **OrchestrationStep** that includes `Order="2"` in the user journey.
2. Add the following **ClaimsExchange** element making sure that you use the same value for the ID that you used for **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Update the value of **TechnicalProfileReferenceId** to the ID of the technical profile you created earlier. For example, `Contoso-SAML2`.

3. Save the *TrustFrameworkExtensions.xml* file and upload it again for verification.


## Configure an AD FS relying party trust

To use AD FS as an identity provider in Azure AD B2C, you need to create an AD FS Relying Party Trust with the Azure AD B2C SAML metadata. The following example shows a URL address to the SAML metadata of an Azure AD B2C technical profile:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Replace the following values:

- **your-tenant** with your tenant name, such as your-tenant.onmicrosoft.com.
- **your-policy** with your policy name. For example, B2C_1A_signup_signin_adfs.
- **your-technical-profile** with the name of your SAML identity provider technical profile. For example, Contoso-SAML2.

Open a browser and navigate to the URL. Make sure you type the correct URL and that you have access to the XML metadata file. To add a new relying party trust by using the AD FS Management snap-in and manually configure the settings, perform the following procedure on a federation server. Membership in **Administrators** or equivalent on the local computer is the minimum required to complete this procedure.

1. In Server Manager, select **Tools**, and then select **AD FS Management**.
2. Select **Add Relying Party Trust**.
3. On the **Welcome** page, choose **Claims aware**, and then click **Start**.
4. On the **Select Data Source** page, select **Import data about the relying party publish online or on a local network**, provide your Azure AD B2C metadata URL, and then click **Next**.
5. On the **Specify Display Name** page, enter a **Display name**, under **Notes**, enter a description for this relying party trust, and then click **Next**.
6. On the **Choose Access Control Policy** page, select a policy, and then click **Next**.
7. On the **Ready to Add Trust** page, review the settings, and then click **Next** to save your relying party trust information.
8. On the **Finish** page, click **Close**, this action automatically displays the **Edit Claim Rules** dialog box.
9. Select **Add Rule**.
10. In **Claim rule template**, select **Send LDAP attributes as claims**.
11. Provide a **Claim rule name**. For the **Attribute store**, select **Select Active Directory**, add the following claims, then click **Finish** and **OK**.

    | LDAP attribute | Outgoing claim type |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    Note that these names will not display in the outgoing claim type dropdown. You need to manually type them in. (The dropdown is actually editable).

12.  Based on your certificate type, you may need to set the HASH algorithm. On the relying party trust (B2C Demo) properties window, select the **Advanced** tab and change the **Secure hash algorithm** to `SHA-256`, and click **Ok**.
13. In Server Manager, select **Tools**, and then select **AD FS Management**.
14. Select the relying party trust you created, select **Update from Federation Metadata**, and then click **Update**.

### Update and test the relying party file

Update the relying party (RP) file that initiates the user journey that you created.

1. Make a copy of *SignUpOrSignIn.xml* in your working directory, and rename it. For example, rename it to *SignUpSignInADFS.xml*.
2. Open the new file and update the value of the **PolicyId** attribute for **TrustFrameworkPolicy** with a unique value. For example, `SignUpSignInADFS`.
3. Update the value of **PublicPolicyUri** with the URI for the policy. For example,`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Update the value of the **ReferenceId** attribute in **DefaultUserJourney** to match the ID of the new user journey that you created (SignUpSignInADFS).
5. Save your changes, upload the file, and then select the new policy in the list.
6. Make sure that Azure AD B2C application that you created is selected in the **Select application** field, and then test it by clicking **Run now**.

## Troubleshooting AD FS service  

AD FS is configured to use the Windows application log. If you experience challenges setting up AD FS as a SAML identity provider using custom policies in Azure AD B2C, you may want to check the AD FS event log:

1. On the Windows **Search bar**, type **Event Viewer**, and then select the **Event Viewer** desktop app.
1. To view the log of a different computer, right-click **Event Viewer (local)**. Select **Connect to another computer**, and fill in the fields to complete the **Select Computer** dialog box.
1. In **Event Viewer**, open the **Applications and Services Logs**.
1. Select **AD FS**, then select **Admin**. 
1. To view more information about an event, double-click the event.  

### SAML request is not signed with expected signature algorithm event

This error indicates that the SAML request sent by Azure AD B2C is not signed with the expected signature algorithm configured in AD FS. For example, the SAML request is signed with the signature algorithm `rsa-sha256`, but the expected signature algorithm is `rsa-sha1`. To fix this issue, make sure both Azure AD B2C and AD FS are configured with the same signature algorithm.

#### Option 1: Set the signature algorithm in Azure AD B2C  

You can configure how to sign the SAML request in Azure AD B2C. The [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) metadata controls the value of the `SigAlg` parameter (query string or post parameter) in the SAML request. The following example configures Azure AD B2C to use the `rsa-sha256` signature algorithm.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### Option 2: Set the signature algorithm in AD FS 

Alternatively, you can configure the expected the SAML request signature algorithm in AD FS.

1. In Server Manager, select **Tools**, and then select **AD FS Management**.
1. Select the **Relying Party Trust** you created earlier.
1. Select **Properties**, then select **Advance**
1. Configure the **Secure hash algorithm**, and select **OK** to save the changes.

::: zone-end