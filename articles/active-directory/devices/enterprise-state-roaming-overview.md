---
title: What is enterprise state roaming in Azure Active Directory? | Microsoft Docs
description: Enterprise State Roaming provides users with a unified experience across their Windows devices and reduces the time needed for configuring a new device.

services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na

ms.collection: M365-identity-device-management
---
# What is enterprise state roaming?

With Windows 10, [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) users gain the ability to securely synchronize their user settings and application settings data to the cloud. Enterprise State Roaming provides users with a unified experience across their Windows devices and reduces the time needed for configuring a new device. Enterprise State Roaming operates similar to the standard [consumer settings sync](https://go.microsoft.com/fwlink/?linkid=2015135) that was first introduced in Windows 8. Additionally, Enterprise State Roaming offers:

* **Separation of corporate and consumer data** – Organizations are in control of their data, and there is no mixing of corporate data in a consumer cloud account or consumer data in an enterprise cloud account.
* **Enhanced security** – Data is automatically encrypted before leaving the user’s Windows 10 device by using Azure Rights Management (Azure RMS), and data stays encrypted at rest in the cloud. All content stays encrypted at rest in the cloud, except for the namespaces, like settings names and Windows app names.  
* **Better management and monitoring** – Provides control and visibility over who syncs settings in your organization and on which devices through the Azure AD portal integration. 

Enterprise State Roaming is available in multiple Azure regions. You can find the updated list of available regions on the [Azure Services by Regions](https://azure.microsoft.com/regions/#services) page under Azure Active Directory.

| Article | Description |
| --- | --- |
| [Enable Enterprise State Roaming in Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming is available to any organization with a Premium Azure Active Directory (Azure AD) subscription. For more details on how to get an Azure AD subscription, see the [Azure AD product](https://azure.microsoft.com/services/active-directory) page. |
| [Settings and data roaming FAQ](enterprise-state-roaming-faqs.md) |This topic answers some questions IT administrators might have about settings and app data sync. |
| [Group policy and MDM settings for settings sync](enterprise-state-roaming-group-policy-settings.md) |Windows 10 provides Group Policy and mobile device management (MDM) policy settings to limit settings sync. |
| [Windows 10 roaming settings reference](enterprise-state-roaming-windows-settings-reference.md) |The following is a complete list of all the settings that will be roamed and/or backed-up in Windows 10. |
| [Troubleshooting](enterprise-state-roaming-troubleshooting.md) |This topic goes through some basic steps for troubleshooting, and contains a list of known issues. |

## Next steps

For information about enabling enterprise state roaming, see [enable enterprise state roaming](enterprise-state-roaming-enable.md).
