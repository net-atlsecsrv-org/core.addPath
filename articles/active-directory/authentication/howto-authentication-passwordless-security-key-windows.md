---
title: Passwordless security key sign-in Windows - Azure Active Directory
description: Enable passwordless security key sign-in to Azure AD using FIDO2 security keys (preview)

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo

ms.collection: M365-identity-device-management
---
# Enable passwordless security key sign in to Windows 10 devices (preview)

This document focuses on enabling FIDO2 security key based passwordless authentication with Windows 10 devices. At the end of this article, you will be able to sign in to both web-based applications and your Azure AD joined Windows 10 devices with your Azure AD account using a FIDO2 security key.

|     |
| --- |
| FIDO2 security keys are a public preview feature of Azure Active Directory. For more information about previews, see  [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## Requirements

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Combined security information registration preview](concept-registration-mfa-sspr-combined.md)
- Compatible [FIDO2 security keys](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN requires Windows 10 version 1809 or higher
- [Azure AD joined devices](../devices/concept-azure-ad-join.md) require Windows 10 version 1809 or higher
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (Optional)
- Provisioning package (Optional)

### Unsupported scenarios

- Windows Server Active Directory Domain Services (AD DS) domain-joined (on-premises only devices) deployment **not supported**.
- RDP, VDI, and Citrix scenarios are **not supported** using security key.
- S/MIME is **not supported** using security key.
- “Run as“ is **not supported** using security key.
- Log in to a server using security key is **not supported**.
- If you have not used your security key to sign in to your device while online, you will not be able to use it to sign in or unlock offline.
- Signing in or unlocking a Windows 10 device with a security key containing multiple Azure AD accounts. This scenario will utilize the last account added to the security key. WebAuthN will allow users to choose the account they wish to use.

## Prepare devices for preview

Azure AD joined devices that you will be piloting with must be running Windows 10 version 1809 or higher. The best experience is on Windows 10 version 1903 or higher.

## Enable security keys for Windows sign-in

Organizations may choose to use one or more of the following methods to enable the use of security keys for Windows sign-in based on their organization's requirements.

- [Enable with Intune](#enable-with-intune)
   - [Targeted Intune deployment](#targeted-intune-deployment)
- [Enable with a provisioning package](#enable-with-a-provisioning-package)

### Enable with Intune

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Browse to **Microsoft Intune** > **Device enrollment** > **Windows enrollment** > **Windows Hello for Business** > **Properties**.
1. Under **Settings** set **Use security keys for sign-in** to **Enabled**.

Configuration of security keys for sign-in, is not dependent on configuring Windows Hello for Business.

#### Targeted Intune deployment

To target specific device groups to enable the credential provider, use the following custom settings via Intune.

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Browse to **Microsoft Intune** > **Device configuration** > **Profiles** > **Create profile**.
1. Configure the new profile with the following settings
   1. Name: Security Keys for Windows Sign-In
   1. Description: Enables FIDO Security Keys to be used during Windows Sign In
   1. Platform: Windows 10 and later
   1. Profile type: Custom
   1. Custom OMA-URI Settings:
      1. Name: Turn on FIDO Security Keys for Windows Sign-In
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Data Type: Integer
      1. Value: 1
1. This policy can be assigned to specific users, devices, or groups. More information can be found in the article [Assign user and device profiles in Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Intune custom device configuration policy creation](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### Enable with a provisioning package

For devices not managed by Intune, a provisioning package can be installed to enable the functionality. The Windows Configuration Designer app can be installed from the [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22).

1. Launch the Windows Configuration Designer.
1. Select **File** > **New project**.
1. Give your project a name and take note of the path where your project is created.
1. Select **Next**.
1. Leave **Provisioning package** selected as the **Selected project workflow** and select **Next**.
1. Select **All Windows desktop editions** under **Choose which settings to view and configure** and select **Next**.
1. Select **Finish**.
1. In your newly created project, browse to **Runtime settings** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Set **UseSecurityKeyForSignIn** to **Enabled**.
1. Select **Export** > **Provisioning package**
1. Leave the defaults in the **Build** window under **Describe the provisioning package** and select **Next**.
1. Leave the defaults in the **Build** window under **Select security details for the provisioning package** and select **Next**.
1. Take note of or change the path in the **Build** windows under **Select where to save the provisioning package** and select **Next**.
1. Select **Build** on the **Build the provisioning package** page.
1. Save the two files created (ppkg and cat) to a location where you can apply them to machines later.
1. Follow the guidance in the article [Apply a provisioning package](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), to apply the provisioning package you created.

> [!NOTE]
> Devices running Windows 10 Version 1809 must also enable shared PC mode (EnableSharedPCMode). Information about enabling this funtionality can be found in the article, 
[Set up a shared or guest PC with Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## Sign in to Windows with a FIDO2 security key

In the example below a user Bala Sandhu has already provisioned their FIDO2 security key using the steps in the previous article, [Enable passwordless security key sign in](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala can choose the security key credential provider from the Windows 10 lock screen and insert the security key to sign into Windows.

![Security key sign in at the Windows 10 lock screen](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### Manage security key biometric, PIN, or reset security key

* Windows 10 version 1903 or higher
   * Users can open **Windows Settings** on their device > **Accounts** > **Security Key**
   * Users can change their PIN, update biometrics, or reset their security key

## Troubleshooting and feedback

If you would like to share feedback or encounter issues while previewing this feature, please share via the Windows Feedback Hub app.

1. Launch **Feedback Hub** and make sure you're signed in.
1. Submit feedback under the following categorization:
   1. Category: Security and Privacy
   1. Subcategory: FIDO
1. To capture logs, use the option: **Recreate my Problem**

## Frequently asked questions

### Does this work in my on-premises environment?

This feature does not work for a pure on-premises Active Directory Domain Services (AD DS) environment.

### My organization requires two factor authentication to access resources, what can I do to support this requirement?

Security keys come in a variety of form factors. Please contact the device manufacturer of interest to discuss how their devices can be enabled with a PIN or biometric as a second factor.

### Can admins set up security keys?

We are working on this capability for general availability (GA) of this feature.

### Where can I go to find compliant security keys?

[FIDO2 security keys](concept-authentication-passwordless.md#fido2-security-keys)

### What do I do if I lose my security key?

You can remove keys from the Azure portal, by navigating to the security info page and removing the security key.

## Next steps

[Learn more about device registration](../devices/overview.md)

[Learn more about Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
