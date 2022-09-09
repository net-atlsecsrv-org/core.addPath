---
title: Prevent attacks using smart lockout - Azure Active Directory
description: Learn how Azure Active Directory smart lockout helps protect your organization from brute-force attacks that try to guess user passwords.

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020

ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya

ms.collection: M365-identity-device-management
---
# Protect user accounts from attacks with Azure Active Directory smart lockout

Smart lockout helps lock out bad actors that try to guess your users' passwords or use brute-force methods to get in. Smart lockout can recognize sign-ins that come from valid users and treat them differently than ones of attackers and other unknown sources. Attackers get locked out, while your users continue to access their accounts and be productive.

## How smart lockout works

By default, smart lockout locks the account from sign-in attempts for one minute after 10 failed attempts. The account locks again after each subsequent failed sign-in attempt, for one minute at first and longer in subsequent attempts. To minimize the ways an attacker could work around this behavior, we don't disclose the rate at which the lockout period grows over additional unsuccessful sign-in attempts.

Smart lockout tracks the last three bad password hashes to avoid incrementing the lockout counter for the same password. If someone enters the same bad password multiple times, this behavior won't cause the account to lock out.

> [!NOTE]
> Hash tracking functionality isn't available for customers with pass-through authentication enabled as authentication happens on-premises not in the cloud.

Federated deployments that use AD FS 2016 and AF FS 2019 can enable similar benefits using [AD FS Extranet Lockout and Extranet Smart Lockout](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Smart lockout is always on, for all Azure AD customers, with these default settings that offer the right mix of security and usability. Customization of the smart lockout settings, with values specific to your organization, requires Azure AD Premium P1 or higher licenses for your users.

Using smart lockout doesn't guarantee that a genuine user is never locked out. When smart lockout locks a user account, we try our best to not lock out the genuine user. The lockout service attempts to ensure that bad actors can't gain access to a genuine user account. The following considerations apply:

* Each Azure AD data center tracks lockout independently. A user has (*threshold_limit * datacenter_count*) number of attempts, if the user hits each data center.
* Smart Lockout uses familiar location vs unfamiliar location to differentiate between a bad actor and the genuine user. Unfamiliar and familiar locations both have separate lockout counters.

Smart lockout can be integrated with hybrid deployments that use password hash sync or pass-through authentication to protect on-premises Active Directory Domain Services (AD DS) accounts from being locked out by attackers. By setting smart lockout policies in Azure AD appropriately, attacks can be filtered out before they reach on-premises AD DS.

When using [pass-through authentication](../hybrid/how-to-connect-pta.md), the following considerations apply:

* The Azure AD lockout threshold is **less** than the AD DS account lockout threshold. Set the values so that the AD DS account lockout threshold is at least two or three times longer than the Azure AD lockout threshold.
* The Azure AD lockout duration must be set longer than the AD DS reset account lockout counter after duration. The Azure AD duration is set in seconds, while the AD duration is set in minutes.

For example, if you want your Azure AD counter to be higher than AD DS, then Azure AD would be 120 seconds (2 minutes) while your on-premises AD is set to 1 minute (60 seconds).

> [!IMPORTANT]
> Currently, an administrator can't unlock the users' cloud accounts if they have been locked out by the Smart Lockout capability. The administrator must wait for the lockout duration to expire. However, the user can unlock by using self-service password reset (SSPR) from a trusted device or location.

## Verify on-premises account lockout policy

To verify your on-premises AD DS account lockout policy, complete the following steps from a domain-joined system with administrator privileges:

1. Open the Group Policy Management tool.
2. Edit the group policy that includes your organization's account lockout policy, such as, the **Default Domain Policy**.
3. Browse to **Computer Configuration** > **Policies** > **Windows Settings** > **Security Settings** > **Account Policies** > **Account Lockout Policy**.
4. Verify your **Account lockout threshold** and **Reset account lockout counter after** values.

![Modify the on-premises Active Directory account lockout policy](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## Manage Azure AD smart lockout values

Based on your organizational requirements, you can customize the Azure AD smart lockout values. Customization of the smart lockout settings, with values specific to your organization, requires Azure AD Premium P1 or higher licenses for your users.

To check or modify the smart lockout values for your organization, complete the following steps:

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Search for and select *Azure Active Directory*, then select **Security** > **Authentication methods** > **Password protection**.
1. Set the **Lockout threshold**, based on how many failed sign-ins are allowed on an account before its first lockout.

    The default is 10.

1. Set the **Lockout duration in seconds**, to the length in seconds of each lockout.

    The default is 60 seconds (one minute).

> [!NOTE]
> If the first sign-in after a lockout also fails, the account locks out again. If an account locks repeatedly, the lockout duration increases.

![Customize the Azure AD smart lockout policy in the Azure portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## How to determine if the Smart lockout feature is working or not

When the smart lockout threshold is triggered, you will get the following message while the account is locked:

*Your account is temporarily locked to prevent unauthorized use. Try again later, and if you still have trouble, contact your admin.*

## Next steps

To customize the experience further, you can [configure custom banned passwords for Azure AD password protection](tutorial-configure-custom-password-protection.md).

To help users reset or change their password from a web browser, you can [configure Azure AD self-service password reset](tutorial-enable-sspr.md).
