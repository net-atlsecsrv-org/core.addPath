---
title: Configure password complexity requirements
titleSuffix: Azure AD B2C
description: How to configure complexity requirements for passwords supplied by consumers in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
---

# Configure complexity requirements for passwords in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) supports changing the complexity requirements for passwords supplied by an end user when creating an account. By default, Azure AD B2C uses **Strong** passwords. Azure AD B2C also supports configuration options to control the complexity of passwords that customers can use.

## Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## Password rule enforcement

During sign-up or password reset, an end user must supply a password that meets the complexity rules. Password complexity rules are enforced per user flow. It is possible to have one user flow require a four-digit pin during sign-up while another user flow requires an eight character string during sign-up. For example, you may use a user flow with different password complexity for adults than for children.

Password complexity is never enforced during sign-in. Users are never prompted during sign-in to change their password because it doesn't meet the current complexity requirement.

Password complexity can be configured in the following types of user flows:

- Sign-up or Sign-in user flow
- Password Reset user flow

If you are using custom policies, you can ([configure password complexity in a custom policy](password-complexity.md)).

## Configure password complexity

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Select the **Directory + Subscription** icon in the portal toolbar, and then select the directory that contains your Azure AD B2C tenant.
3. In the Azure portal, search for and select **Azure AD B2C**.
4. Select **User flows**.
2. Select a user flow, and click **Properties**.
3. Under **Password complexity**, change the password complexity for this user flow to **Simple**, **Strong**, or **Custom**.

### Comparison Chart

| Complexity | Description |
| --- | --- |
| Simple | A password that is at least 8 to 64 characters. |
| Strong | A password that is at least 8 to 64 characters. It requires 3 out of 4 of lowercase, uppercase, numbers, or symbols. |
| Custom | This option provides the most control over password complexity rules.  It allows configuring a custom length.  It also allows accepting number-only passwords (pins). |

## Custom options

### Character Set

Allows you to accept digits only (pins) or the full character set.

- **Numbers only** allows digits only (0-9) while entering a password.
- **All** allows any letter, number, or symbol.

### Length

Allows you to control the length requirements of the password.

- **Minimum Length** must be at least 4.
- **Maximum Length** must be greater or equal to minimum length and at most can be 64 characters.

### Character classes

Allows you to control the different character types used in the password.

- **2 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** ensures the password contains at least two character types. For example, a number and a lowercase character.
- **3 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** ensures the password contains at least three character types. For example, a number, a lowercase character and an uppercase character.
- **4 of 4: Lowercase character, Uppercase character, Number (0-9), Symbol** ensures the password contains all for character types.

    > [!NOTE]
    > Requiring **4 of 4** can result in end-user frustration. Some studies have shown that this requirement does not improve password entropy. See [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## Password predicate validation

To configure the password complexity, override the `newPassword` and `reenterPassword` [claim types](claimsschema.md) with a reference to [predicate validations](predicates.md#predicatevalidations). The PredicateValidations element groups a set of predicates to form a user input validation that can be applied to a claim type. Open the extensions file of your policy. For example, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Search for the [BuildingBlocks](buildingblocks.md) element. If the element doesn't exist, add it.
1. Locate the [ClaimsSchema](claimsschema.md) element. If the element doesn't exist, add it.
1. Add the `newPassword` and `reenterPassword` claims to the **ClaimsSchema** element.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predicates](predicates.md) defines a basic validation to check the value of a claim type and returns true or false. The validation is done by using a specified method element, and a set of parameters relevant to the method. Add the following predicates to the **BuildingBlocks** element, immediately after the closing of the `</ClaimsSchema>` element:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Add the following predicate validations to the **BuildingBlocks** element, immediately after the closing of the `</Predicates>` element:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

## Disable strong password 

The following technical profiles are [Active Directory technical profiles](active-directory-technical-profile.md), which read and write data to Azure Active Directory. Override these technical profiles in the extension file. Use `PersistedClaims` to disable the strong password policy. Find the **ClaimsProviders** element.  Add the following claim providers as follows:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Save the policy file.

## Test your policy

### Upload the files

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Make sure you're using the directory that contains your Azure AD B2C tenant by selecting the **Directory + subscription** filter in the top menu and choosing the directory that contains your tenant.
3. Choose **All services** in the top-left corner of the Azure portal, and then search for and select **Azure AD B2C**.
4. Select **Identity Experience Framework**.
5. On the Custom Policies page, click **Upload Policy**.
6. Select **Overwrite the policy if it exists**, and then search for and select the *TrustFrameworkExtensions.xml* file.
7. Click **Upload**.

### Run the policy

1. Open the sign-up or sign-in policy. For example, *B2C_1A_signup_signin*.
2. For **Application**, select your application that you previously registered. To see the token, the **Reply URL** should show `https://jwt.ms`.
3. Click **Run now**.
4. Select **Sign up now**, enter an email address, and enter a new password. Guidance is presented on password restrictions. Finish entering the user information, and then click **Create**. You should see the contents of the token that was returned.

## Next steps

- Learn how to [Configure password change in Azure Active Directory B2C](add-password-change-policy.md).
- Learn more about the [Predicates](predicates.md) and [PredicateValidations](predicates.md#predicatevalidations) elements in the IEF reference.


::: zone-end