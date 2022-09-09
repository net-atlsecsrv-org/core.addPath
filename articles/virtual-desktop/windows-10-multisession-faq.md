---
title: Windows 10 Enterprise multi-session FAQ - Azure
description: Frequently asked questions and best practices for using Windows 10 Enterprise multi-session for Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
---
# Windows 10 Enterprise multi-session FAQ

This article answers frequently asked questions and explains best practices for Windows 10 Enterprise multi-session.
 
## What is Windows 10 Enterprise multi-session?

Windows 10 Enterprise multi-session, formerly known as Windows 10 Enterprise for Virtual Desktops (EVD), is a new Remote Desktop Session Host that allows multiple concurrent interactive sessions. Previously, only Windows Server could do this. This capability gives users a familiar Windows 10 experience while IT can benefit from the cost advantages of multi-session and use existing per-user Windows licensing instead of RDS Client Access Licenses (CALs). For more information about licenses and pricing, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## How many users can simultaneously have an interactive session on Windows 10 Enterprise multi-session?

How many interactive sessions that can be active at the same time relies on your system's hardware resources (vCPU, memory, disk, and vGPU), how your users use their apps while signed in to a session, and how heavy your system's workload is. We suggest you validate your system's performance to understand how many users you can have on Windows 10 Enterprise multi-session. To learn more, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## Why does my application report Windows 10 Enterprise multi-session as a Server operating system?

Windows 10 Enterprise multi-session is a virtual edition of Windows 10 Enterprise. One of the differences is that this operating system (OS) reports the [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) as having a value of 3, the same value as Windows Server. This property keeps the OS compatible with existing RDSH management tooling, RDSH multi-session-aware applications, and mostly low-level system performance optimizations for RDSH environments. Some application installers can block installation on Windows 10 multi-session depending on whether they detect the ProductType is set to Client. If your app won't install, contact your application vendor for an updated version. 
 
## Can I run Windows 10 Enterprise multi-session on-premises?

Windows 10 Enterprise multi-session can't run in on-premises production environments because it's optimized for the Windows Virtual Desktop service for Azure. It's against the licensing agreement to run Windows 10 Enterprise multi-session outside of Azure for production purposes. Windows 10 Enterprise multi-session won't activate against on-premises Key Management Services (KMS).
 
## How do I customize the Windows 10 Enterprise multi-session image for my organization?

You can start a virtual machine (VM) in Azure with Windows 10 Windows 10 Enterprise multi-session and customize it by installing LOB applications, sysprep/generalize, and then create an image using the Azure portal.  
 
To get started, create a VM in Azure with Windows 10 Windows 10 Enterprise multi-session. Instead of starting the VM in Azure, you can download the VHD directly. After that, you'll be able to use the VHD you downloaded to create a new Generation 1 VM on a Windows 10 PC with Hyper-V enabled.

Customize the image to your needs by installing LOB applications and sysprep the image. When you're done customizing, upload the image to Azure with the VHD inside. After that, get Windows Virtual Desktop from the Azure Marketplace and use it to deploy a new host pool with the customized image.
 
## How do I manage Windows 10 Enterprise multi-session after deployment?

You can use any supported configuration tool, but we recommend Configuration Manager version 1906 because it supports Windows 10 Enterprise multi-session. We're currently working on Microsoft Intune support.
 
## Can Windows 10 Enterprise multi-session be Azure Active Directory (AD)-joined?

Windows 10 Enterprise multi-session is currently supported to be hybrid Azure AD-joined. After Windows 10 Enterprise multi-session is domain-joined, use the existing Group Policy Object to enable Azure AD registration. For more information, see [Plan your hybrid Azure Active Directory join implementation](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## Where can I find the Windows 10 Enterprise multi-session image?

Windows 10 Enterprise multi-session is in the Azure gallery. To find it, navigate to the Azure portal and search for the Windows 10 Enterprise for Virtual Desktops release. For an image integrated with Office Pro Plus, go to the Azure portal and search for Microsoft Windows 10 + Office 365 ProPlus.

## Which Windows 10 Enterprise multi-session image should I use?

The Azure gallery has several releases, including Windows 10 Enterprise multi-session, version 1809, and Windows 10 Enterprise multi-session, version 1903. We recommend using the latest version for improved performance and reliability.
 
## Which Windows 10 Enterprise multi-session versions are supported?

Windows 10 Enterprise multi-session, versions 1809 and later are supported and are available in the Azure gallery. These releases follow the same support life-cycle policy as Windows 10 Enterprise, which means the spring release is supported for 18 months and the fall release for 30 months.
 
## Which profile management solution should I use for Windows 10 Enterprise multi-session?

We recommend you use FSLogix profile containers when you configure Windows 10 Enterprise in non-persistent environments or other scenarios that need a centrally stored profile. FSLogix ensures the user profile is available and up-to-date for every user session. We also recommend you use your FSLogix profile container to store a user profile in any SMB share with appropriate permissions, but you can store user profiles in Azure page blob storage if necessary. Windows Virtual Desktop users can use FSLogix at no additional cost.
 
For more information about how to configure an FSLogix profile container, see [Configure the FSLogix profile container](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## Which license do I need to access Windows 10 Enterprise multi-session?

For a full list of applicable licenses, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## Why do my apps disappear after I sign out?

This happens because you're using Windows 10 Enterprise multi-session with a profile management solution like FSLogix. Your admin or profile solution configured your system to delete user profiles when users sign out. This configuration means that when your system deletes your user profile after you sign out, it also removes any apps you installed during your session. If you want to keep the apps you installed, you'll need to ask your admin to provision these apps for all users in your Windows Virtual Desktop environment.

## How do I make sure apps don't disappear when users sign out?

Most virtualized environments are configured by default to prevent users from installing additional apps to their profiles. If you want to make sure an app doesn't disappear when your user signs out of Windows Virtual Desktop, you have to provision that app for all user profiles in your environment. For more information about provisioning apps, check out these resources:

- [Publish built-in apps in Windows Virtual Desktop](publish-apps.md)
- [DISM app package servicing command-line options](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## How do I make sure users don't download and install apps from the Microsoft Store?

You can disable the Microsoft Store app to make sure users don't download extra apps beyond the apps you've already provisioned for them.

To disable the Store app:

1. Create a new Group Policy.
2. Select **Computer Configuration** > **Administrative Templates** > **Windows Components**.
3. Select **Store**.
4. Select **Store Application**.
5. Select **Disabled**, then select **OK**.
6. Select **Apply**.
 
## Next steps

To learn more about Windows Virtual Desktop and Windows 10 Enterprise multi-session:

- Read our [Windows Virtual Desktop Preview documentation](overview.md)
- Visit our [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Set up your Windows Virtual Desktop deployment with the [Windows Virtual Desktop tutorials](tenant-setup-azure-active-directory.md)
