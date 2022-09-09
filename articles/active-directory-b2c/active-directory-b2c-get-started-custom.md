---
title: Get started with custom policies - Azure Active Directory B2C
description: Learn how to get started with custom policies in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
---

# Get started with custom policies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Custom policies](active-directory-b2c-overview-custom.md) are configuration files that define the behavior of your Azure Active Directory B2C (Azure AD B2C) tenant. In this article, you create a custom policy that supports local account sign-up or sign-in by using an email address and password. You also prepare your environment for adding identity providers.

## Prerequisites

- If you don't have one already, [create an Azure AD B2C tenant](tutorial-create-tenant.md) that is linked to your Azure subscription.
- [Register your application](tutorial-register-applications.md) in the tenant that you created so that it can communicate with Azure AD B2C.
- Complete the steps in [Set up sign-up and sign-in with a Facebook account](active-directory-b2c-setup-fb-app.md) to configure a Facebook application.

## Add signing and encryption keys

1. Sign in to the [Azure portal](https://portal.azure.com)
1. Use the **Directory + subscription** filter in the top menu to select the directory that contains your Azure AD B2C tenant.
1. In the left menu, select **Azure AD B2C**. Or, select **All services** and search for and select **Azure AD B2C**.
1. On the Overview page, select **Identity Experience Framework**.

### Create the signing key

1. Select **Policy Keys** and then select **Add**.
1. For **Options**, choose `Generate`.
1. In **Name**, enter `TokenSigningKeyContainer`. The prefix `B2C_1A_` might be added automatically.
1. For **Key type**, select **RSA**.
1. For **Key usage**, select **Signature**.
1. Select **Create**.

### Create the encryption key

1. Select **Policy Keys** and then select **Add**.
1. For **Options**, choose `Generate`.
1. In **Name**, enter `TokenEncryptionKeyContainer`. The prefix `B2C_1A`_ might be added automatically.
1. For **Key type**, select **RSA**.
1. For **Key usage**, select **Encryption**.
1. Select **Create**.

### Create the Facebook key

Add your Facebook application's [App Secret](active-directory-b2c-setup-fb-app.md) as a policy key. You can use the App Secret of the application you created as part of this article's prerequisites.

1. Select **Policy Keys** and then select **Add**.
1. For **Options**, choose `Manual`.
1. For **Name**, enter `FacebookSecret`. The prefix `B2C_1A_` might be added automatically.
1. In **Secret**, enter your Facebook application's *App Secret* from developers.facebook.com. This value is the secret, not the application ID.
1. For **Key usage**, select **Signature**.
1. Select **Create**.

## Register Identity Experience Framework applications

Azure AD B2C requires you to register two applications that are used to sign up and sign in users: IdentityExperienceFramework (a web app), and ProxyIdentityExperienceFramework (a native app) with delegated permission from the IdentityExperienceFramework app. Local accounts exist only in your tenant. Your users sign up with a unique email address/password combination to access your tenant-registered applications.

### Register the IdentityExperienceFramework application

1. Select **All services** in the top-left corner of the Azure portal.
1. In the search box, enter `Azure Active Directory`.
1. Select **Azure Active Directory** in the search results.
1. Under **Manage** in the left-hand menu, select **App registrations (Legacy)**.
1. Select **New application registration**.
1. For **Name**, enter `IdentityExperienceFramework`.
1. For **Application type**, choose **Web app/API**.
1. For **Sign-on URL**, enter `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, where `your-tenant-name` is your Azure AD B2C tenant domain name. All URLs should now be using [b2clogin.com](b2clogin.md).
1. Select **Create**. After it's created, copy the application ID and save it to use later.

### Register the ProxyIdentityExperienceFramework application

1. In **App registrations (Legacy)**, select **New application registration**.
1. For **Name**, enter `ProxyIdentityExperienceFramework`.
1. For **Application type**, choose **Native**.
1. For **Redirect URI**, enter `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, where `your-tenant-name` is your Azure AD B2C tenant.
1. Select **Create**. After it's created, copy the application ID and save it to use later.
1. Select **Settings**, then select **Required permissions**, and then select **Add**.
1. Choose **Select an API**, search for and select **IdentityExperienceFramework**, and then click **Select**.
1. Select the check box next to **Access IdentityExperienceFramework**, click **Select**, and then click **Done**.
1. Select **Grant permissions**, and then confirm by selecting **Yes**.

## Custom policy starter pack

Custom policies are a set of XML files you upload to your Azure AD B2C tenant to define technical profiles and user journeys. We provide starter packs with several pre-built policies to get you going quickly. Each of these starter packs contains the smallest number of technical profiles and user journeys needed to achieve the scenarios described:

- **LocalAccounts** - Enables the use of local accounts only.
- **SocialAccounts** - Enables the use of social (or federated) accounts only.
- **SocialAndLocalAccounts** - Enables the use of both local and social accounts.
- **SocialAndLocalAccountsWithMFA** - Enables social, local, and multi-factor authentication options.

Each starter pack contains:

- **Base file** - Few modifications are required to the base. Example: *TrustFrameworkBase.xml*
- **Extension file** - This file is where most configuration changes are made. Example: *TrustFrameworkExtensions.xml*
- **Relying party files** - Task-specific files called by your application. Examples: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In this article, you edit the XML custom policy files in the **SocialAndLocalAccounts** starter pack. If you need an XML editor, try [Visual Studio Code](https://code.visualstudio.com/download), a lightweight cross-platform editor.

### Get the starter pack

Get the custom policy starter packs from GitHub, then update the XML files in the SocialAndLocalAccounts starter pack with your Azure AD B2C tenant name.

1. [Download the .zip file](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) or clone the repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In all of the files in the **SocialAndLocalAccounts** directory, replace the string `yourtenant` with the name of your Azure AD B2C tenant.

    For example, if the name of your B2C tenant is *contosotenant*, all instances of `yourtenant.onmicrosoft.com` become `contosotenant.onmicrosoft.com`.

### Add application IDs to the custom policy

Add the application IDs to the extensions file *TrustFrameworkExtensions.xml*.

1. Open `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** and find the element `<TechnicalProfile Id="login-NonInteractive">`.
1. Replace both instances of `IdentityExperienceFrameworkAppId` with the application ID of the IdentityExperienceFramework application that you created earlier.
1. Replace both instances of `ProxyIdentityExperienceFrameworkAppId` with the application ID of the ProxyIdentityExperienceFramework application that you created earlier.
1. Save the file.

## Upload the policies

1. Select the **Identity Experience Framework** menu item in your B2C tenant in the Azure portal.
1. Select **Upload custom policy**.
1. In this order, upload the policy files:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

As you upload the files, Azure adds the prefix `B2C_1A_` to each.

> [!TIP]
> If your XML editor supports validation, validate the files against the `TrustFrameworkPolicy_0.3.0.0.xsd` XML schema that is located in the root directory of the starter pack. XML schema validation identifies errors before uploading.

## Test the custom policy

1. Under **Custom policies**, select **B2C_1A_signup_signin**.
1. For **Select application** on the overview page of the custom policy, select the web application named *webapp1* that you previously registered.
1. Make sure that the **Reply URL** is `https://jwt.ms`.
1. Select **Run now**.
1. Sign up using an email address.
1. Select **Run now** again.
1. Sign in with the same account to confirm that you have the correct configuration.

## Add Facebook as an identity provider

1. In the `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** file, replace the value of `client_id` with the Facebook application ID:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Upload the *TrustFrameworkExtensions.xml* file to your tenant.
1. Under **Custom policies**, select **B2C_1A_signup_signin**.
1. Select **Run now** and select Facebook to sign in with Facebook and test the custom policy.

## Next steps

Next, try adding Azure Active Directory (Azure AD) as an identity provider. The base file used in this getting started guide already contains some of the content that you need for adding other identity providers like Azure AD.

For information about setting up Azure AD as and identity provider, see [Set up sign-up and sign-in with an Azure Active Directory account using Active Directory B2C custom policies](active-directory-b2c-setup-aad-custom.md).
