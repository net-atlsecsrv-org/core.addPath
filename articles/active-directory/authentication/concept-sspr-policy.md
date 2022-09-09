---
title: Self-service password reset policies - Azure Active Directory
description: Learn about the different Azure Active Directory self-service password reset policy options

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020

ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
---
# Self-service password reset policies and restrictions in Azure Active Directory

This article describes the password policies and complexity requirements associated with user accounts in your Azure Active Directory (Azure AD) tenant.

## Administrator reset policy differences

**Microsoft enforces a strong default *two-gate* password reset policy for any Azure administrator role**. This policy may be different from the one you have defined for your users, and this policy can't be changed. You should always test password reset functionality as a user without any Azure administrator roles assigned.

With a two-gate policy, **administrators don't have the ability to use security questions**.

The two-gate policy requires two pieces of authentication data, such as an *email address*, *authenticator app*, or a *phone number*. A two-gate policy applies in the following circumstances:

* All the following Azure administrator roles are affected:
  * Helpdesk administrator
  * Service support administrator
  * Billing administrator
  * Partner Tier1 Support
  * Partner Tier2 Support
  * Exchange administrator
  * Skype for Business administrator
  * User administrator
  * Directory writers
  * Global administrator or company administrator
  * SharePoint administrator
  * Compliance administrator
  * Application administrator
  * Security administrator
  * Privileged role administrator
  * Intune administrator
  * Application proxy service administrator
  * Dynamics 365 administrator
  * Power BI service administrator
  * Authentication administrator
  * Privileged Authentication administrator

* If 30 days have elapsed in a trial subscription; or
* A custom domain has been configured for your Azure AD tenant, such as *contoso.com*; or
* Azure AD Connect is synchronizing identities from your on-premises directory

### Exceptions

A one-gate policy requires one piece of authentication data, such as an email address or phone number. A one-gate policy applies in the following circumstances:

* It's within the first 30 days of a trial subscription; or
* A custom domain hasn't been configured for your Azure AD tenant so is using the default **.onmicrosoft.com*. The default **.onmicrosoft.com* domain isn't recommended for production use; and
* Azure AD Connect isn't synchronizing identities

## UserPrincipalName policies that apply to all user accounts

Every user account that needs to sign in to Azure AD must have a unique user principal name (UPN) attribute value associated with their account. The following table outlines the policies that apply to both on-premises Active Directory Domain Services user accounts that are synchronized to the cloud and to cloud-only user accounts:

| Property | UserPrincipalName requirements |
| --- | --- |
| Characters allowed |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Characters not allowed |<ul> <li>Any "\@\" character that's not separating the username from the domain.</li> <li>Can't contain a period character "." immediately preceding the "\@\" symbol</li></ul> |
| Length constraints |<ul> <li>The total length must not exceed 113 characters</li><li>There can be up to 64 characters before the "\@\" symbol</li><li>There can be up to 48 characters after the "\@\" symbol</li></ul> |

## Password policies that only apply to cloud user accounts

The following table describes the password policy settings applied to user accounts that are created and managed in Azure AD:

| Property | Requirements |
| --- | --- |
| Characters allowed |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ;</li> <li>blank space</li></ul> |
| Characters not allowed | Unicode characters. |
| Password restrictions |<ul><li>A minimum of 8 characters and a maximum of 256 characters.</li><li>Requires three out of four of the following:<ul><li>Lowercase characters.</li><li>Uppercase characters.</li><li>Numbers (0-9).</li><li>Symbols (see the previous password restrictions).</li></ul></li></ul> |
| Password expiry duration (Maximum password age) |<ul><li>Default value: **90** days.</li><li>The value is configurable by using the `Set-MsolPasswordPolicy` cmdlet from the Azure Active Directory Module for Windows PowerShell.</li></ul> |
| Password expiry notification (When users are notified of password expiration) |<ul><li>Default value: **14** days (before password expires).</li><li>The value is configurable by using the `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Password expiry (Let passwords never expire) |<ul><li>Default value: **false** (indicates that password's have an expiration date).</li><li>The value can be configured for individual user accounts by using the `Set-MsolUser` cmdlet.</li></ul> |
| Password change history | The last password *can't* be used again when the user changes a password. |
| Password reset history | The last password *can* be used again when the user resets a forgotten password. |
| Account lockout | After 10 unsuccessful sign-in attempts with the wrong password, the user is locked out for one minute. Further incorrect sign-in attempts lock out the user for increasing durations of time. [Smart lockout](howto-password-smart-lockout.md) tracks the last three bad password hashes to avoid incrementing the lockout counter for the same password. If someone enters the same bad password multiple times, this behavior will not cause the account to lock out. |

## Set password expiration policies in Azure AD

A *global administrator* or *user administrator* for a Microsoft cloud service can use the *Microsoft Azure AD Module for Windows PowerShell* to set user passwords not to expire. You can also use Windows PowerShell cmdlets to remove the never-expires configuration or to see which user passwords are set to never expire.

This guidance applies to other providers, such as Intune and Office 365, which also rely on Azure AD for identity and directory services. Password expiration is the only part of the policy that can be changed.

> [!NOTE]
> Only passwords for user accounts that are not synchronized through directory synchronization can be configured to not expire. For more information about directory synchronization, see [Connect AD with Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## Set or check the password policies by using PowerShell

To get started, [download and install the Azure AD PowerShell module](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) and [connect it to your Azure AD tenant](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples). After the module is installed, use the following steps to configure each field.

### Check the expiration policy for a password

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Run one of the following commands:

   * To see if a single user's password is set to never expire, run the following cmdlet by using the UPN (for example, *aprilr\@contoso.onmicrosoft.com*) or the user ID of the user you want to check:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * To see the **Password never expires** setting for all users, run the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### Set a password to expire

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Execute one of the following commands:

   * To set the password of one user so that the password expires, run the following cmdlet by using the UPN or the user ID of the user:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * To set the passwords of all users in the organization so that they expire, use the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### Set a password to never expire

1. Connect to Windows PowerShell by using your user administrator or company administrator credentials.
1. Execute one of the following commands:

   * To set the password of one user to never expire, run the following cmdlet by using the UPN or the user ID of the user:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * To set the passwords of all the users in an organization to never expire, run the following cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Passwords set to `-PasswordPolicies DisablePasswordExpiration` still age based on the `pwdLastSet` attribute. Based on the `pwdLastSet` attribute, if you change the expiration to `-PasswordPolicies None`, all passwords that have a `pwdLastSet` older than 90 days require the user to change them the next time they sign in. This change can affect a large number of users.

## Next steps

To get started with SSPR, see [Tutorial: Enable users to unlock their account or reset passwords using Azure Active Directory self-service password reset](tutorial-enable-sspr.md).

If you or users have problems with SSPR, see [Troubleshoot self-service password reset](active-directory-passwords-troubleshoot.md)
