---
title: 'Azure AD Connect: Hybrid identity considerations for Azure Government cloud'
description: Special considerations for deploying Azure AD Connect with the Azure Government cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Hybrid identity considerations for the Azure Government cloud

This article describes considerations for integrating a hybrid environment with the Microsoft Azure Government cloud. This information is provided as a reference for administrators and architects who work with the Azure Government cloud.

> [!NOTE]
> To integrate an on-premises Microsoft Azure Active Directory (Azure AD) environment with the Azure Government cloud, you need to upgrade to the latest release of [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

For a full list of United States government Department of Defense endpoints, refer to the [documentation](/office365/enterprise/office-365-u-s-government-dod-endpoints).

## Azure AD Pass-through Authentication

The following information describes implementation of Pass-through Authentication and the Azure Government cloud.

### Allow access to URLs

Before you deploy the Pass-through Authentication agent, verify whether a firewall exists between your servers and Azure AD. If your firewall or proxy allows Domain Name System (DNS) blocked or safe programs, add the following connections.

> [!NOTE]
> The following guidance also applies to installing the [Azure AD Application Proxy connector](https://aka.ms/whyappproxy) for Azure Government environments.

|URL |How it's used|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|The agent uses these URLs to communicate with the Azure AD cloud service. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| The agent uses these URLs to verify certificates.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| The agent uses these URLs during the registration process.

### Install the agent for the Azure Government cloud

Follow these steps to install the agent for the Azure Government cloud:

1. In the command-line terminal, go to the folder that contains the executable file that installs the agent.
1. Run the following commands, which specify that the installation is for Azure Government.

   For Pass-through Authentication:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   For Application Proxy:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## Single sign-on

### Set up your Azure AD Connect server

If you use Pass-through Authentication as your sign-on method, no additional prerequisite check is required. If you use password hash synchronization as your sign-on method and there is a firewall between Azure AD Connect and Azure AD, ensure that:

- You use Azure AD Connect version 1.1.644.0 or later.
- If your firewall or proxy allows DNS blocked or safe programs, add the connections to the &#42;.msappproxy.us URLs over port 443.

  If not, allow access to the Azure datacenter IP ranges, which are updated weekly. This prerequisite applies only when you enable the feature. It isn't required for actual user sign-ons.

### Roll out Seamless Single Sign-On

You can gradually roll out Azure AD Seamless Single Sign-On to your users by using the following instructions. You start by adding the Azure AD URL `https://autologon.microsoft.us` to all or selected users' Intranet zone settings by using Group Policy in Active Directory.

You also need to enable the intranet zone policy setting **Allow updates to status bar via script through Group Policy**.

## Browser considerations

### Mozilla Firefox (all platforms)

Mozilla Firefox doesn't automatically use Kerberos authentication. Each user must manually add the Azure AD URL to their Firefox settings by following these steps:

1. Run Firefox and enter **about:config** in the address bar. Dismiss any notifications that you might see.
1. Search for the **network.negotiate-auth.trusted-uris** preference. This preference lists the sites trusted by Firefox for Kerberos authentication.
1. Right-click the preference name and then select **Modify**.
1. Enter `https://autologon.microsoft.us` in the box.
1. Select **OK** and then reopen the browser.

### Microsoft Edge based on Chromium (all platforms)

If you have overridden the `AuthNegotiateDelegateAllowlist` or `AuthServerAllowlist` policy settings in your environment, ensure that you add the Azure AD URL `https://autologon.microsoft.us` to them.

### Google Chrome (all platforms)

If you have overridden the `AuthNegotiateDelegateWhitelist` or `AuthServerWhitelist` policy settings in your environment, ensure that you add the Azure AD URL `https://autologon.microsoft.us` to them.

## Next steps

- [Pass-through Authentication](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-On](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)