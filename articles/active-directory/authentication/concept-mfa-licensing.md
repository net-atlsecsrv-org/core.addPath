---
title: Azure MFA versions and consumption plans - Azure Active Directory
description: Information about the Multi-factor Authentication client and the different methods and versions available. 

services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
---
# How to get Azure Multi-Factor Authentication

When it comes to protecting your accounts, two-step verification should be standard across your organization. This feature is especially important for accounts that have privileged access to resources. For this reason, Microsoft offers basic two-step verification features to Office 365 and Azure Active Directory (Azure AD) Administrators for no extra cost. If you want to upgrade the features for your admins or extend two-step verification to the rest of your users, you can purchase Azure Multi-Factor Authentication in several ways.

> [!IMPORTANT]
> This article is meant to be a guide to help you understand the different ways to buy Azure Multi-Factor Authentication. For specific details about pricing and billing, you should always refer to the [Multi-Factor Authentication pricing page](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## Available versions of Azure Multi-Factor Authentication

The following table describes the differences between versions of multi-factor authentication:

| Version | Description |
| --- | --- |
| Free option | Customers who are utilizing the free benefits of Azure AD can use [security defaults](../conditional-access/concept-conditional-access-security-defaults.md) to enable multi-factor authentication in their environment. |
| Multi-Factor Authentication for Office 365 | This version is managed from the Office 365 or Microsoft 365 portal. Administrators can [secure Office 365 resources with two-step verification](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). This version is part of an Office 365 subscription. |
| Multi-Factor Authentication for Azure AD Administrators | Users assigned the Azure AD Global Administrator role in Azure AD tenants can enable two-step verification at no additional cost. |
| Azure Multi-Factor Authentication | Often referred to as the "full" version, Azure Multi-Factor Authentication offers the richest set of capabilities. It provides additional configuration options via the [Azure portal](https://portal.azure.com), advanced reporting, and support for a range of on-premises and cloud applications. Azure Multi-Factor Authentication is a feature of [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) and [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business). |

> [!NOTE]
> New customers may no longer purchase Azure Multi-Factor Authentication as a standalone offering effective September 1st, 2018. Multi-factor authentication will continue to be available as a feature in Azure AD Premium or Microsoft 365 Business licenses.

## Feature comparison of versions

The following table provides a list of the features that are available in the various versions of Azure Multi-Factor Authentication.

> [!NOTE]
> This comparison table discusses the features that are part of each version of Multi-Factor Authentication. If you have the full Azure Multi-Factor Authentication service, some features may not be available depending on whether you use [MFA in the cloud or MFA on-premises](concept-mfa-whichversion.md).
>

| Feature | Multi-Factor Authentication for Office 365 | Multi-Factor Authentication for Azure AD Administrators | Azure Multi-Factor Authentication | Security defaults | 
| --- |:---:|:---:|:---:|:---:|
| Protect Azure AD admin accounts with MFA |● |● (Azure AD Global Administrator accounts only) |● |● |
| Mobile app as a second factor |● |● |● |● |
| Phone call as a second factor |● |● |● |   |
| SMS as a second factor |● |● |● |   |
| App passwords for clients that don't support MFA |● |● |● |   |
| Admin control over verification methods |● |● |● |   |
| Protect non-admin accounts with MFA |● | |● |● |
| PIN mode | | |● |   |
| Fraud alert | | |● |   |
| MFA Reports | | |● |   |
| One-Time Bypass | | |● |   |
| Custom greetings for phone calls | | |● |   |
| Custom caller ID for phone calls | | |● |   |
| Trusted IPs | | |● |   |
| Remember MFA for trusted devices |● |● |● |   |
| MFA for on-premises applications | | |● |   |

> [!IMPORTANT]
> Starting in March of 2019 the phone call options will not be available to MFA and SSPR users in free/trial Azure AD tenants. SMS messages are not impacted by this change. Phone call will continue to be available to users in paid Azure AD tenants. This change only impacts free/trial Azure AD tenants.

## How to turn on Azure Multi-Factor Authentication for Azure AD Administrators

Users assigned the Global Administrator role in Azure AD tenants can enable two-step verification for their Azure AD Global Admin accounts at no additional cost. If you are using a Microsoft Account, you can register for multi-factor authentication using the guidance found in the Microsoft account support article, [About two-step verification](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). If you are not using a Microsoft Account, turn on multi-factor authentication for Global Admins using the guidance found in the article [How to require two-step verification for a user or group](howto-mfa-userstates.md).

## How to purchase Azure Multi-Factor Authentication

Purchase licenses that include Azure Multi-Factor Authentication, like Azure Active Directory Premium, or a license bundle that includes Azure AD Premium, or Conditional Access and assign them to your users in Azure Active Directory.

### Consumption-based licensing

Consumption-based licensing is no longer available to new customers effective September 1, 2018.

Effective September 1, 2018 new auth providers may no longer be created. Existing auth providers may continue to be used and updated. Multi-factor authentication will continue to be an available feature in Azure AD Premium licenses.

When using an Azure Multi-Factor Authentication Provider, there are two usage models available that are billed through your Azure subscription:

1. **Per Enabled User** - For enterprises that want to enable two-step verification for a fixed number of employees who regularly need authentication. Per-user billing is based on the number of users enabled for MFA in your Azure AD tenant and your Azure MFA Server. If users are enabled for MFA in both Azure AD and Azure MFA Server, and domain sync (Azure AD Connect) is enabled, then we count the larger set of users. If domain sync isn't enabled, then we count the sum of all users enabled for MFA in Azure AD and Azure MFA Server. Billing is prorated and reported to the Commerce system daily.

   > [!NOTE]
   > Billing example 1:
   > You have 5,000 users enabled for MFA today. The MFA system divides that number by 31, and reports 161.29 users for that day. Tomorrow you enable 15 more users, so the MFA system reports 161.77 users for that day. By the end of the billing cycle, the total number of users billed against your Azure subscription adds up to around 5,000.
   >
   > Billing example 2:
   > You have a mixture of users with licenses and users without, so you have a per-user Azure MFA Provider to make up the difference. There are 4,500 Enterprise Mobility + Security licenses on your tenant, but 5,000 users enabled for MFA. Your Azure subscription is billed for 500 users, prorated and reported daily as 16.13 users.
   >

1. **Per Authentication** - For enterprises that want to enable two-step verification for a large group of users who infrequently need authentication. Billing is based on the number of two-step verification requests, regardless of whether those verifications succeed or are denied. This billing appears on your Azure usage statement in packs of 10 authentications, and is reported daily.

   > [!NOTE]
   > Billing example 3:
   > Today, the Azure MFA service received 3,105 two-step verification requests. Your Azure subscription is billed for 310.5 authentication packs.
   >

It's important to note that you can have licenses, but still get billed for consumption-based configuration. If you set up a per-authentication Azure MFA Provider, you are billed for every two-step verification request, even those requests done by users who have licenses. If you set up a per-user Azure MFA Provider on a domain that isn't linked to your Azure AD tenant, you are billed per enabled user even if your users have licenses on Azure AD.

## Next steps

- For more pricing details, see [Azure MFA Pricing](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
