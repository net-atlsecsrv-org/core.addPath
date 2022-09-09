---
title: Azure Security Center search | Microsoft Docs
description: Learn how Azure Security Center uses Azure Monitor logs search to retrieve and analyze your security data.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''

ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin

---
# Azure Security Center search (Retired)

> [!NOTE]
> Security Center's Search dashboard has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_search).

Azure Security Center uses [Azure Monitor logs search](../log-analytics/log-analytics-log-searches.md) to retrieve and analyze your security data. Azure Monitor logs includes a query language to quickly retrieve and consolidate data. From Security Center, you can leverage Azure Monitor logs search to construct queries and analyze collected data.

Search is available in both the Free tier and Standard tier of Security Center.  The data available in your log searches is dependent on the tier level applied to your workspace.  See the Security Center [pricing page](../security-center/security-center-pricing.md) for more information.


> [!NOTE]
> Security Center does not save security data for a workspace under the Free tier. You can send a variety of logs to a workspace under the Free tier and search on that data but search results do not include data from Security Center. Security Center only saves data to a workspace under the Standard tier.
>
>

## Access search
1. Under the Security Center main menu, select **Search**.

   ![Select Log search][1]

2. Security Center lists all workspaces under your Azure subscriptions. Select a workspace. (If you have only one workspace, this workspace selector does not appear.)

   ![Select a workspace][2]

3. **Log Search** opens. To query for more data under the selected workspace, enter this example query:

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   Result shows all accounts that failed to sign in (event 4625).

   ![Search results][3]

See [Kusto query language](../log-analytics/log-analytics-search-reference.md) for more information on how to query for data under the selected workspace.

## Next steps
In this article you learned how to access search in Security Center. Security Center uses Azure Monitor logs search. To learn more about Azure Monitor logs search, see:

- [What is Azure Monitor logs?](../log-analytics/log-analytics-overview.md) – Overview on Azure Monitor logs
- [Understanding log searches in Azure Monitor logs](../log-analytics/log-analytics-log-search-new.md) - Describes how log searches are used in Azure Monitor logs and provides concepts that should be understood before creating a log search
- [Find data using log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md) – Tutorial on using log search
- [Kusto search reference](../log-analytics/log-analytics-search-reference.md) – Describes the query language in Azure Monitor logs

To learn more about Security Center, see:

- [Security Center Overview](security-center-intro.md) – Describes Security Center’s key capabilities

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
