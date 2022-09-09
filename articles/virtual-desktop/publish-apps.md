---
title: Publish built-in apps in Windows Virtual Desktop - Azure
description: How to publish modern apps in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: helohr
---
# Publish built-in apps in Windows Virtual Desktop

This article will tell you how to publish apps in your Windows Virtual Desktop environment.

## Publish built-in apps

To publish a built-in app:

1. Connect to one of the virtual machines in your host pool.
2. Get the **PackageFamilyName** of the app you want to publish by following the instructions in [this article](https://docs.microsoft.com/powershell/module/appx/get-appxpackage?view=win10-ps).
3. Finally, run the following cmdlet with `<PackageFamilyName>` replaced by the **PackageFamilyName** you found in the previous step:
   
   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows Virtual Desktop only supports publishing apps with install locations that begin with `C:\Program Files\Windows Apps`.

## Update app icons

After you publish an app, it will have the default Windows app icon instead of its regular icon picture. To change the icon to its regular icon, put the image of the icon you want on a network share. Supported image formats are PNG, BMP, GIF, JPG, JPEG, and ICO.

## Publish Microsoft Edge

The process you use to publish Microsoft Edge is a little different from the publishing process for other apps. To publish Microsoft Edge with the default homepage, run this cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" 
```

## Next steps

- Learn about how to configure feeds to organize how apps are displayed for users at [Customize feed for Windows Virtual Desktop users](customize-feed-for-virtual-desktop-users.md).
- Learn about the MSIX app attach feature at [Set up MSIX app attach](app-attach.md).

