---
title: Connect Azure AD data to Azure Sentinel | Microsoft Docs
description: Learn how to connect Azure Active Directory data to Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''

ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin

---
# Connect data from Azure Active Directory



Azure Sentinel enables you to collect data from [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) and stream it into Azure Sentinel. You can choose to stream  [sign-in logs](../active-directory/reports-monitoring/concept-sign-ins.md) and [audit logs](../active-directory/reports-monitoring/concept-audit-logs.md) .

## Prerequisites

- If you want to export sign-in data from Active Directory, you must have an Azure AD P1 or P2 license.

- User with global admin or security admin permissions on the tenant you want to stream the logs from.

- To be able to see the connection status, you must have permission to access Azure AD diagnostic logs. 


## Connect to Azure AD

1. In Azure Sentinel, select **Data connectors** and then click the **Azure Active Directory** tile.

1. Next to the logs you want to stream into Azure Sentinel, click **Connect**.

1. You can select whether you want the alerts from Azure AD to automatically generate incidents in Azure Sentinel. Under **Create incidents** select **Enable** to enable the default analytic rule that creates incidents automatically from alerts generated in the connected security service. You can then edit this rule under **Analytics** and then **Active rules**.

1. To use the relevant schema in Log Analytics for the Azure AD alerts, search for **SigninLogs** and **AuditLogs**.




## Next steps
In this document, you learned how to connect Azure AD to Azure Sentinel. To learn more about Azure Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](quickstart-get-visibility.md).
- Get started [detecting threats with Azure Sentinel](tutorial-detect-threats-built-in.md).
