---
title: Windows Virtual Desktop safe URL list - Azure
description: A list of URLs you should unblock to ensure your Windows Virtual Desktop deployment works as intended.
services: virtual-desktop
author: heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
---

# Safe URL list

You'll need to unblock certain URLs so your Windows Virtual Desktop deployment works properly. This article lists these URLs so you know which ones are safe.

## Virtual machines

The Azure virtual machines you create for Windows Virtual Desktop must have access to the following URLs:

|Address|Outbound TCP port|Purpose|Service Tag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Service traffic|WindowsVirtualDesktop|
|mrsglobalsteus2prod.blob.core.windows.net|443|Agent and SXS stack updates|AzureCloud|
|*.core.windows.net|443|Agent traffic|AzureCloud|
|*.servicebus.windows.net|443|Agent traffic|AzureCloud|
|prod.warmpath.msftcloudes.com|443|Agent traffic|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows activation|Internet|
|wvdportalstorageblob.blob.core.windows.net|443|Azure portal support|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata service endpoint](../virtual-machines/windows/instance-metadata-service.md) | N/A |

>[!IMPORTANT]
>Windows Virtual Desktop now supports the FQDN tag. For more information, see [Use Azure Firewall to protect Window Virtual Desktop deployments](../firewall/protect-windows-virtual-desktop.md).
>
>We recommend you use FQDN tags or service tags instead of URLs to prevent service issues. The listed URLs and tags only correspond to Windows Virtual Desktop sites and resources. They don't include URLs for other services like Azure Active Directory.

The following table lists optional URLs that your Azure virtual machines can have access to:

|Address|Outbound TCP port|Purpose|Service Tag|
|---|---|---|---|
|*.microsoftonline.com|443|Authentication to Microsoft Online Services|None|
|*.events.data.microsoft.com|443|Telemetry Service|None|
|www.msftconnecttest.com|443|Detects if the OS is connected to the internet|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|None|
|login.windows.net|443|Sign in to Microsoft Online Services, Microsoft 365|None|
|*.sfx.ms|443|Updates for OneDrive client software|None|
|*.digicert.com|443|Certificate revocation check|None|

>[!NOTE]
>Windows Virtual Desktop currently doesn't have a list of IP address ranges that you can unblock to allow network traffic. We only support unblocking specific URLs at this time.
>
>For a list of safe Office-related URLs, including required Azure Active Directory-related URLs, see [Office 365 URLs and IP address ranges](/office365/enterprise/urls-and-ip-address-ranges).
>
>You must use the wildcard character (*) for URLs involving service traffic. If you prefer to not use * for agent-related traffic, here's how to find the URLs without wildcards:
>
>1. Register your virtual machines to the Windows Virtual Desktop host pool.
>2. Open **Event viewer**, then go to **Windows logs** > **Application** > **WVD-Agent** and look for Event ID 3701.
>3. Whitelist the URLs that you find under Event ID 3701. The URLs under Event ID 3701 are region-specific. You'll need to repeat the unblocking process with the relevant URLs for each region you want to deploy your virtual machines in.

## Remote Desktop clients

Any Remote Desktop clients you use must have access to the following URLs:

|Address|Outbound TCP port|Purpose|Client(s)|
|---|---|---|---|
|*.wvd.microsoft.com|443|Service traffic|All|
|*.servicebus.windows.net|443|Troubleshooting data|All|
|go.microsoft.com|443|Microsoft FWLinks|All|
|aka.ms|443|Microsoft URL shortener|All|
|docs.microsoft.com|443|Documentation|All|
|privacy.microsoft.com|443|Privacy statement|All|
|query.prod.cms.rt.microsoft.com|443|Client updates|Windows Desktop|

>[!IMPORTANT]
>Opening these URLs is essential for a reliable client experience. Blocking access to these URLs is unsupported and will affect service functionality. These URLs only correspond to the client sites and resources, and don't include URLs for other services like Azure Active Directory.
