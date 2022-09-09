---
title: Use a SDK to configure managed identities on a VM - Azure AD
description: Step by step instructions for configuring and using managed identities for Azure resources on an Azure VM, using an Azure SDK.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''

ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
---

# Configure a VM with managed identities for Azure resources using an Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed identities for Azure resources provides Azure services with an automatically managed identity in Azure Active Directory (AD). You can use this identity to authenticate to any service that supports Azure AD authentication, without having credentials in your code. 

In this article, you learn how to enable and remove managed identities for Azure resources for an Azure VM, using an Azure SDK.

## Prerequisites

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## Azure SDKs with managed identities for Azure resources support 

Azure supports multiple programming platforms through a series of [Azure SDKs](https://azure.microsoft.com/downloads). Several of them have been updated to support managed identities for Azure resources, and provide corresponding samples to demonstrate usage. This list is updated as additional support is added:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Manage resource from a VM enabled with managed identities for Azure resources enabled](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Manage storage from a VM enabled with managed identities for Azure resources](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Create a VM with system-assigned managed identity enabled](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Create a VM with system-assigned managed identity enabled](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Create Azure VM with an system-assigned identity enabled](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## Next steps

- See related articles under **Configure Identity for an Azure VM**, to learn how you can also use the Azure portal, PowerShell, CLI, and resource templates.
