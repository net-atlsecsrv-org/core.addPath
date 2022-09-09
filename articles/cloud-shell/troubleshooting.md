---
title: Azure Cloud Shell troubleshooting | Microsoft Docs
description: Troubleshooting Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
 
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
---

# Troubleshooting & Limitations of Azure Cloud Shell

Known resolutions for troubleshooting issues in Azure Cloud Shell include:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## General troubleshooting

### Error running AzureAD cmdlets in PowerShell

- **Details**: When you run AzureAD cmdlets like `Get-AzureADUser` in Cloud Shell, you might see an error: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`. 
- **Resolution**: Run the `Connect-AzureAD` cmdlet. Previously, Cloud Shell ran this cmdlet automatically during PowerShell startup. To speed up start time, the cmdlet no longer runs automatically. You can choose to restore the previous behavior by adding `Connect-AzureAD` to the $PROFILE file in PowerShell.

### Early timeouts in FireFox

- **Details**: Cloud Shell utilizes an open websocket to pass input/output to your browser. FireFox has preset policies that can close the websocket prematurely causing early timeouts in Cloud Shell.
- **Resolution**: Open FireFox and navigate to "about:config" in the URL box. Search for "network.websocket.timeout.ping.request" and change the value from 0 to 10.

### Disabling Cloud Shell in a locked down network environment

- **Details**: Administrators may wish to disable access to Cloud Shell for their users. Cloud Shell utilizes access to the `ux.console.azure.com` domain, which can be denied, stopping any access to Cloud Shell's entrypoints including portal.azure.com, shell.azure.com, Visual Studio Code Azure Account extension, and docs.microsoft.com. In the US Government cloud, the entrypoint is `ux.console.azure.us`; there is no corresponding shell.azure.us.
- **Resolution**: Restrict access to `ux.console.azure.com` or `ux.console.azure.us` via network settings to your environment. The Cloud Shell icon will still exist in the Azure portal, but will not successfully connect to the service.

### Storage Dialog - Error: 403 RequestDisallowedByPolicy

- **Details**: When creating a storage account through Cloud Shell, it is unsuccessful due to an Azure Policy assignment placed by your admin. Error message will include: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolution**: Contact your Azure administrator to remove or update the Azure Policy assignment denying storage creation.

### Storage Dialog - Error: 400 DisallowedOperation

- **Details**: When using an Azure Active Directory subscription, you cannot create storage.
- **Resolution**: Use an Azure subscription capable of creating storage resources. Azure AD subscriptions are not able to create Azure resources.

### Terminal output - Error: Failed to connect terminal: websocket cannot be established. Press `Enter` to reconnect.
- **Details**: Cloud Shell requires the ability to establish a websocket connection to Cloud Shell infrastructure.
- **Resolution**: Check you have configured your network settings to enable sending https requests and websocket requests to domains at *.console.azure.com.

### Set your Cloud Shell connection to support using TLS 1.2
 - **Details**: To define the version of TLS for your connection to Cloud Shell, you must set browser-specific settings.
 - **Resolution**: Navigate to the security settings of your browser and select the checkbox next to "Use TLS 1.2".

## Bash troubleshooting

### Cannot run the docker daemon

- **Details**: Cloud Shell utilizes a container to host your shell environment, as a result running the daemon is disallowed.
- **Resolution**: Utilize [docker-machine](https://docs.docker.com/machine/overview/), which is installed by default, to manage docker containers from a remote Docker host.

## PowerShell troubleshooting

### GUI applications are not supported

- **Details**: If a user launches a GUI application, the prompt does not return. For example, when one clone a private GitHub repo that has two factor authentication enabled, a dialog box is displayed for completing the two factor authentication.
- **Resolution**: Close and reopen the shell.

### Troubleshooting remote management of Azure VMs
> [!NOTE]
> Azure VMs must have a Public facing IP address.

- **Details**: Due to the default Windows Firewall settings for WinRM the user may see the following error:
 `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolution**:  Run `Enable-AzVMPSRemoting` to enable all aspects of PowerShell remoting on the target machine.

### `dir` does not update the result in Azure drive

- **Details**: By default, to optimize for user experience, the results of `dir` is cached in Azure drive.
- **Resolution**: After you create, update or remove an Azure resource, run `dir -force` to update the results in the Azure drive.

## General limitations

Azure Cloud Shell has the following known limitations:

### Quota limitations

Azure Cloud Shell has a limit of 20 concurrent users per tenant per region. If you try to open more simultaneous sessions than the limit, you will see an "Tenant User Over Quota" error. If you have a legitimate need to have more sessions open than this (for example for training sessions), contact support in advance of your anticipated usage to request a quota increase.

Cloud Shell is provided as a free service and is designed to be used to configure your Azure environment, not as a general purpose computing platform. Excessive automated usage may be considered in breach to the Azure Terms of Service and could lead to Cloud Shell access being blocked.

### System state and persistence

The machine that provides your Cloud Shell session is temporary, and it is recycled after your session is inactive for 20 minutes. Cloud Shell requires an Azure file share to be mounted. As a result, your subscription must be able to set up storage resources to access Cloud Shell. Other considerations include:

- With mounted storage, only modifications within the `clouddrive` directory are persisted. In Bash, your `$HOME` directory is also persisted.
- Azure file shares can be mounted only from within your [assigned region](persisting-shell-storage.md#mount-a-new-clouddrive).
  - In Bash, run `env` to find your region set as `ACC_LOCATION`.
- Azure Files supports only locally redundant storage and geo-redundant storage accounts.

### Browser support

Cloud Shell supports the latest versions of following browsers:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari in private mode is not supported.

### Copy and paste

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### Usage limits

Cloud Shell is intended for interactive use cases. As a result, any long-running non-interactive sessions are ended without warning.

### User permissions

Permissions are set as regular users without sudo access. Any installation outside your `$Home` directory is not persisted.

## Bash limitations

### Editing .bashrc

Take caution when editing .bashrc, doing so can cause unexpected errors in Cloud Shell.

## PowerShell limitations

### Preview version of AzureAD module

Currently, `AzureAD.Standard.Preview`, a preview version of .NET Standard-based, module is available. This module provides the same functionality as `AzureAD`.

## Personal data in Cloud Shell

Azure Cloud Shell takes your personal data seriously, the data captured and stored by the Azure Cloud Shell service are used to provide defaults for your experience such as your most recently used shell, preferred font size, preferred font type, and file share details that back cloud drive. Should you wish to export or delete this data, use the following instructions.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### Export
In order to **export** the user settings Cloud Shell saves for you such as preferred shell, font size, and font type run the following commands.

1. [![Image showing a button labeled Launch Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Run the following commands in Bash or PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### Delete
In order to **delete** your user settings Cloud Shell saves for you such as preferred shell, font size, and font type run the following commands. The next time you start Cloud Shell you will be asked to onboard a file share again. 

>[!Note]
> If you delete your user settings, the actual Azure Files share will not be deleted. Go to your Azure Files to complete that action.

1. [![Image showing a button labeled Launch Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

2. Run the following commands in Bash or PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## Azure Government limitations
Azure Cloud Shell in Azure Government is only accessible through the Azure portal.

>[!Note]
> Connecting to GCC-High or Government DoD Clouds for Exchange Online is currently not supported.
