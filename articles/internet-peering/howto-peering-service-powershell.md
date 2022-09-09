---
title: Enable Azure Peering Service on a Direct peering by using PowerShell
titleSuffix: Azure
description: Enable Azure Peering Service on a Direct peering by using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
---

# Enable Azure Peering Service on a Direct peering by using PowerShell

This article describes how to enable Azure [Peering Service](overview-peering-service.md) on a Direct peering by using PowerShell cmdlets and the Azure Resource Manager deployment model.

If you prefer, you can complete this guide by using the Azure [portal](howto-peering-service-portal.md).

## Before you begin
* Review the [prerequisites](prerequisites.md) before you begin configuration.
* Choose a Direct peering in your subscription for which you want to enable Peering Service. If you don't have one, either convert a legacy Direct peering or create a new Direct peering:
    * To convert a legacy Direct peering, follow the instructions in [Convert a legacy Direct peering to an Azure resource by using PowerShell](howto-legacy-direct-powershell.md).
    * To create a new Direct peering, follow the instructions in [Create or modify a Direct peering by using PowerShell](howto-direct-powershell.md).

### Work with Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## Enable Peering Service on a Direct peering

### <a name= get></a>View Direct peering
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Enable the Direct peering for Peering Service

After you get Direct peering in the previous step, enable it for Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## Modify a Direct peering connection

If you need to modify connection settings, see the "Modify a Direct peering" section in [Create or modify a Direct peering by using PowerShell](howto-direct-powershell.md).

## Next steps

* [Create or modify Exchange peering by using PowerShell](howto-exchange-powershell.md)
* [Convert a legacy Exchange peering to an Azure resource by using PowerShell](howto-legacy-exchange-powershell.md)

## Additional resources
You can get detailed descriptions of all the parameters by running the following command:

```powershell
Get-Help Get-AzPeering -detailed
```

For frequently asked questions, see the [Peering Service FAQ](service-faqs.md).