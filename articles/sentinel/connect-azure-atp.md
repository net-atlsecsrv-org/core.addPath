---
title: Connect Azure ATP data to Azure Sentinel| Microsoft Docs
description: Learn how to connect Azure ATP data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin

---
# Connect data from Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> The Azure Advanced Threat Protection data connector in Azure Sentinel is currently in public preview.
> This feature is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

You can stream logs from [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) into Azure Sentinel with a single click.

## Prerequisites

- User with global administrator or security administrator permissions
- You must be a preview customer of Azure ATP and enable integration between Azure ATP and Microsoft Cloud App Security. For more information, see [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## Connect to Azure ATP

Make sure the Azure ATP preview version is [enabled on your network](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
If Azure ATP is deployed and ingesting your data, the suspicious alerts can easily be streamed into Azure Sentinel. It may take up to 24 hours for the alerts to start streaming into Azure Sentinel.


1. To connect Azure ATP to Azure Sentinel, you must first enable integration between Azure ATP and Microsoft Cloud App Security. For information on how to do this, see [Azure Advanced Threat Protection integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. In Azure Sentinel, select **Data connectors** and then click the **Azure Advanced Threat Protection (Preview)** tile.

1. You can select whether you want the alerts from Azure ATP to automatically generate incidents in Azure Sentinel automatically. Under **Create incidents** select **Enable** to enable the default analytic rule that creates incidents automatically from alerts generated in the connected security service. You can then edit this rule under **Analytics** and then **Active rules**.

1. Click **Connect**.

1. To use the relevant schema in Log Analytics for the Azure ATP alerts, search for **SecurityAlert**.

> [!NOTE]
> If the alerts are larger than 30 KB, Azure Sentinel stops displaying the Entities field in the alerts.

## Next steps
In this document, you learned how to connect Azure Advanced Threat Protection to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats-built-in.md).

