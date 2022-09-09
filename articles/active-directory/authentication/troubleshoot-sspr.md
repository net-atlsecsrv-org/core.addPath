---
title: Troubleshoot self-service password reset - Azure Active Directory
description: Learn how to troubleshoot common problems and resolution steps for self-service password reset in Azure Active Directory

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020

ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock

ms.collection: M365-identity-device-management
---
# Troubleshoot self-service password reset in Azure Active Directory

Azure Active Directory (Azure AD) self-service password reset (SSPR) lets users reset their passwords in the cloud.

If you have problems with SSPR, the following troubleshooting steps and common errors may help. If you can't find the answer to your problem, [our support teams are always available](#contact-microsoft-support) to assist you further.

## SSPR configuration in the Azure portal

If you have problems seeing or configuring SSPR options in the Azure portal, review the following troubleshooting steps:

### I don't see the **Password reset** section under Azure AD in the Azure portal.

You won't see if **Password reset** menu option if you don't have an Azure AD license assigned to the administrator performing the operation.

To assign a license to the administrator account in question, follow the steps to [Assign, verify, and resolve problems with licenses](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### I don't see a particular configuration option.

Many elements of the UI are hidden until they're needed. Make sure the option is enabled before you look for the specific configuration options.

### I don't see the **On-premises integration** tab.

On-premises password writeback is only visible if you've downloaded Azure AD Connect and have configured the feature.

For more information, see [Getting started with Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## SSPR reporting

If you have problems with SSPR reporting in the Azure portal, review the following troubleshooting steps:

### I don't see any password management activity types in the **Self-Service Password Management** audit event category.

This can happen if you don't have an Azure AD license assigned to the administrator performing the operation.

To assign a license to the administrator account in question, follow the steps to [Assign, verify, and resolve problems with licenses](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### User registrations show multiple times.

When a user registers, we currently log each individual piece of data that's registered as a separate event.

If you want to aggregate this data and have greater flexibility in how you can view it, you can download the report and open the data as a pivot table in Excel.

## SSPR registration portal

If your users have problems registering for SSPR, review the following troubleshooting steps:

### The directory isn't enabled for password reset. The user may see an error that reports, "Your administrator has not enabled you to use this feature."

You can enable SSPR for all users, no users, or for selected groups of users. Only one Azure AD group can currently be enabled for SSPR using the Azure portal. As part of a wider deployment of SSPR, nested groups are supported. Make sure that the users in the group(s) you choose have the appropriate licenses assigned.

In the Azure portal, change the **Self-service password reset enabled** configuration to *Selected* or *All* and then select **Save**.

### The user doesn't have an Azure AD license assigned. The user may see an error that reports, "Your administrator has not enabled you to use this feature."

Only one Azure AD group can currently be enabled for SSPR using the Azure portal. As part of a wider deployment of SSPR, nested groups are supported. Make sure that the users in the group(s) you choose have the appropriate licenses assigned. Review the previous troubleshooting step to enable SSPR as required.

Also review troubleshooting steps to make sure that the administrator performing the configuration options has a license assigned. To assign a license to the administrator account in question, follow the steps to [Assign, verify, and resolve problems with licenses](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### There's an error processing the request.

Generic SSPR registration errors can be caused by many issues, but generally this error is caused by either a service outage or a configuration issue. If you continue to see this generic error when you retry the SSPR registration process, [contact Microsoft support](#contact-microsoft-support) for additional assistance.

## SSPR usage

If you or your users have problems using SSPR, review the following troubleshooting scenarios and resolution steps:

| Error | Solution |
| --- | --- |
| The directory isn't enabled for password reset. | In the Azure portal, change the **Self-service password reset enabled** configuration to *Selected* or *All* and then select **Save**. |
| The user doesn't have an Azure AD license assigned. | This can happen if you don't have an Azure AD license assigned to the desired user. To assign a license to the administrator account in question, follow the steps to [Assign, verify, and resolve problems with licenses](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| The directory is enabled for password reset, but the user has missing or malformed authentication information. | Make sure that user has properly formed contact data on file in the directory. For more information, see [Data used by Azure AD self-service password reset](howto-sspr-authenticationdata.md). |
| The directory is enabled for password reset, but the user has only one piece of contact data on file when the policy is set to require two verification methods. | Make sure that the user has at least two properly configured contact methods. An example is having both a mobile phone number *and* an office phone number. |
| The directory is enabled for password reset and the user is properly configured, but the user is unable to be contacted. | This can be the result of a temporary service error or if there's incorrect contact data that we can't properly detect. <br> <br> If the user waits 10 seconds, a link is displayed to "Try again" and "Contact your administrator". If the user selects "Try again," it retries the call. If the user selects "Contact your administrator," it sends a form email to the administrators requesting a password reset to be performed for that user account. |
| The user never receives the password reset SMS or phone call. | This can be the result of a malformed phone number in the directory. Make sure the phone number is in the format "+1 4251234567". <br> <br>Password reset doesn't support extensions, even if you specify one in the directory. The extensions are stripped before the call is made. Use a number without an extension, or integrate the extension into the phone number in your private branch exchange (PBX). |
| The user never receives the password reset email. | The most common cause for this problem is that the message is rejected by a spam filter. Check your spam, junk, or deleted items folder for the email. <br> <br> Also, make sure the user checks the correct email account as registered with SSPR. |
| I've set a password reset policy, but when an admin account uses password reset, that policy isn't applied. | Microsoft manages and controls the administrator password reset policy to ensure the highest level of security. |
| The user is prevented from attempting a password reset too many times in a day. | An automatic throttling mechanism is used to block users from attempting to reset their passwords too many times in a short period of time. Throttling occurs the following scenarios: <br><ul><li>The user attempts to validate a phone number five times in one hour.</li><li>The user attempts to use the security questions gate five times in one hour.</li><li>The user attempts to reset a password for the same user account five times in one hour.</li></ul>If a user encounters this problem, they must wait 24 hours after the last attempt. The user can then reset their password. |
| The user sees an error when validating their phone number. | This error occurs when the phone number entered doesn't match the phone number on file. Make sure the user is entering the complete phone number, including the area and country code, when they attempt to use a phone-based method for password reset. |
| There's an error processing the request. | Generic SSPR registration errors can be caused by many issues, but generally this error is caused by either a service outage or a configuration issue. If you continue to see this generic error when you re-try the SSPR registration process, [contact Microsoft support](#contact-microsoft-support) for additional assistance. |
| On-premises policy violation | The password doesn't meet the on-premises Active Directory password policy. The user must define a password that meets the complexity or strength requirements. |
| Password doesn't comply with fuzzy policy | The password that was used appears in the [banned password list](./concept-password-ban-bad.md#how-are-passwords-evaluated) and can't be used. The user must define a password that meets or exceeds the banned password list policy. |

## SSPR errors that a user might see

The following errors and technical details may be shown to a user as part of the SSPR process. Often, the error isn't something they can resolve themselves, as the SSPR feature needs to enabled, configured, or registered for their account.

Use the following information to understand the problem and what needs to be corrected on the Azure AD tenant or individual user account.

| Error | Details | Technical details |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | We're sorry, you can't reset your password at this time because your administrator has disabled password reset for your organization. There is no further action you can take to resolve this situation. Please contact your admin and ask them to enable this feature.<br /><br />To learn more, see [Help, I forgot my Azure AD password](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: We've detected that password reset has not been enabled by your administrator. Please contact your admin and ask them to enable password reset for your organization. |
| WritebackNotEnabled = 10 |We're sorry, you can't reset your password at this time because your administrator has not enabled a necessary service for your organization. There is no further action you can take to resolve this situation. Please contact your admin and ask them to check your organization's configuration.<br /><br />To learn more about this necessary service, see [Configuring password writeback](./tutorial-enable-sspr-writeback.md). | SSPR_0010: We've detected that password writeback has not been enabled. Please contact your admin and ask them to enable password writeback. |
| SsprNotEnabledInUserPolicy = 11 | We're sorry, you can't reset your password at this time because your administrator has not configured password reset for your organization. There is no further action you can take to resolve this situation. Contact your admin and ask them to configure password reset.<br /><br />To learn more about password reset configuration, see [Quickstart: Azure AD self-service password reset](./tutorial-enable-sspr.md). | SSPR_0011: Your organization has not defined a password reset policy. Please contact your admin and ask them to define a password reset policy. |
| UserNotLicensed = 12 | We're sorry, you can't reset your password at this time because required licenses are missing from your organization. There is no further action you can take to resolve this situation. Please contact your admin and ask them to check your license assignment.<br /><br />To learn more about licensing, see [Licensing requirements for Azure AD self-service password reset](./concept-sspr-licensing.md). | SSPR_0012: Your organization does not have the required licenses necessary to perform password reset. Please contact your admin and ask them to review the license assignments. |
| UserNotMemberOfScopedAccessGroup = 13 | We're sorry, you can't reset your password at this time because your administrator has not configured your account to use password reset. There is no further action you can take to resolve this situation. Please contact your admin and ask them to configure your account for password reset.<br /><br />To learn more about account configuration for password reset, see [Roll out password reset for users](./howto-sspr-deployment.md). | SSPR_0013: You are not a member of a group enabled for password reset. Contact your admin and request to be added to the group. |
| UserNotProperlyConfigured = 14 | We're sorry, you can't reset your password at this time because necessary information is missing from your account. There is no further action you can take to resolve this situation. Please contact you admin and ask them to reset your password for you. After you have access to your account again, you need to register the necessary information.<br /><br />To register information, follow the steps in the [Register for self-service password reset](../user-help/active-directory-passwords-reset-register.md) article. | SSPR_0014: Additional security info is needed to reset your password. To proceed, contact your admin and ask them to reset your password. After you have access to your account, you can register additional security info at https://aka.ms/ssprsetup. Your admin can add additional security info to your account by following the steps in [Set and read authentication data for password reset](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | We're sorry, we can't reset your password at this time because of a problem with your organization's password reset configuration. There is no further action you can take to resolve this situation. Please contact your admin and ask them to investigate. <br /><br />Or<br /><br />We cannot reset your password at this time because of a problem with your organization's password reset configuration. There is no further action you can take to resolve this issue. Please contact your admin and ask them to investigate.<br /><br />To learn more about the potential problem, see [Troubleshoot password writeback](troubleshoot-sspr-writeback.md). | SSPR_0029: We are unable to reset your password due to an error in your on-premises configuration. Please contact your admin and ask them to investigate. |
| OnPremisesConnectivityError = 30 | We're sorry, we can't reset your password at this time because of connectivity issues to your organization. There is no action to take right now, but the problem might be resolved if you try again later. If the problem persists, please contact your admin and ask them to investigate.<br /><br />To learn more about connectivity issues, see [Troubleshoot password writeback connectivity](troubleshoot-sspr-writeback.md). | SSPR_0030: We can't reset your password due to a poor connection with your on-premises environment. Contact your admin and ask them to investigate.|

## Azure AD forums

If you have general questions about Azure AD and self-service password reset, you can ask the community for assistance on the [Microsoft Q&A question page for Azure Active Directory](/answers/topics/azure-active-directory.html). Members of the community include engineers, product managers, MVPs, and fellow IT professionals.

## Contact Microsoft support

If you can't find the answer to a problem, our support teams are always available to assist you further.

To properly assist you, we ask that you provide as much detail as possible when opening a case. These details include the following:

* **General description of the error**: What is the error? What was the behavior that was noticed? How can we reproduce the error? Provide as much detail as possible.
* **Page**: What page were you on when you noticed the error? Include the URL if you're able to and a screenshot of the page.
* **Support code**: What was the support code that was generated when the user saw the error?
   * To find this code, reproduce the error, then select the **Support code** link at the bottom of the screen and send the support engineer the GUID that results.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="The support code is located at the bottom right of the web browser window.":::

  * If you're on a page without a support code at the bottom, select F12 and search for the SID and CID and send those two results to the support engineer.
* **Date, time, and time zone**: Include the precise date and time *with the time zone* that the error occurred.
* **User ID**: Who was the user who saw the error? An example is *user\@contoso.com*.
   * Is this a federated user?
   * Is this a pass-through authentication user?
   * Is this a password-hash-synchronized user?
   * Is this a cloud-only user?
* **Licensing**: Does the user have an Azure AD license assigned?
* **Application event log**: If you're using password writeback and the error is in your on-premises infrastructure, include a zipped copy of your application event log from the Azure AD Connect server.

## Next steps

To learn more about SSPR, see [How it works: Azure AD self-service password reset](concept-sspr-howitworks.md) or [How does self-service password reset writeback work in Azure AD?](concept-sspr-writeback.md).
