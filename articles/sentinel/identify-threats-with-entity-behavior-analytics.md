---
title: Identify advanced threats with User and Entity Behavior Analytics (UEBA) in Azure Sentinel | Microsoft Docs
description:  Create behavioral baselines for entities (users, hostnames, IP addresses) and use them to detect anomalous behavior and identify zero-day advanced persistent threats (APT).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin

---
# Identify advanced threats with User and Entity Behavior Analytics (UEBA) in Azure Sentinel

## What is User and Entity Behavior Analytics (UEBA)?

### The concept

Identifying threats inside your organization and their potential impact - whether a compromised entity or a malicious insider - has always been a time-consuming and labor-intensive process. Sifting through alerts, connecting the dots, and active hunting all add up to massive amounts of time and effort expended with minimal returns, and the possibility of sophisticated threats simply evading discovery. Particularly elusive threats like zero-day, targeted, and advanced persistent threats can be the most dangerous to your organization, making their detection all the more critical.

The UEBA capability in Azure Sentinel eliminates the drudgery from your analysts’ workloads and the uncertainty from their efforts, and delivers high-fidelity, actionable intelligence, so they can focus on investigation and remediation.

As Azure Sentinel collects logs and alerts from all of its connected data sources, it analyzes them and builds baseline behavioral profiles of your organization’s entities (users, hosts, IP addresses, applications etc.) across time and peer group horizon. Using a variety of techniques and machine learning capabilities, Sentinel can then identify anomalous activity and help you determine if an asset has been compromised. Not only that, but it can also figure out the relative sensitivity of particular assets, identify peer groups of assets, and evaluate the potential impact of any given compromised asset (its “blast radius”). Armed with this information, you can effectively prioritize your investigation and incident handling. 

### Architecture overview

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Entity behavior analytics architecture":::

### Security-driven analytics

Inspired by Gartner’s paradigm for UEBA solutions, Azure Sentinel provides an "outside-in" approach, based on three frames of reference:

- **Use cases:** By prioritizing for relevant attack vectors and scenarios based on security research aligned with the MITRE ATT&CK framework of tactics, techniques, and sub-techniques that puts various entities as victims, perpetrators, or pivot points in the kill chain; Azure Sentinel focuses specifically on the most valuable logs each data source can provide.

- **Data Sources:** While first and foremost supporting Azure data sources, Azure Sentinel thoughtfully selects third-party data sources to provide data that matches our threat scenarios.

- **Analytics:** Using various machine learning (ML) algorithms, Azure Sentinel identifies anomalous activities and presents evidence clearly and concisely in the form of contextual enrichments, some examples of which appear below.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Behavior analytics outside-in approach":::

Azure Sentinel presents artifacts that help your security analysts get a clear understanding of anomalous activities in context, and in comparison with the user's baseline profile. Actions performed by a user (or a host, or an address) are evaluated contextually, where a "true" outcome indicates an identified anomaly:
- across geographical locations, devices, and environments.
- across time and frequency horizons (compared to user's own history).
- as compared to peers' behavior.
- as compared to organization's behavior.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Entity context":::


### Scoring

Each activity is scored with “Investigation Priority Score” – which determine the probability of a specific user performing a specific activity, based on behavioral learning of the user and their peers. Activities identified as the most abnormal receive the highest scores (on a scale of 0-10).

See how behavior analytics is used in [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) for an example of how this works.



## Entity pages

When you encounter any entity (currently limited to users and hosts) in a search, an alert, or an investigation, you can select the entity and be taken to an **entity page**, a datasheet full of useful information about that entity. The types of information you will find on this page include basic facts about the entity, a timeline of notable events related to this entity and insights about the entity's behavior.
 
Entity pages consist of three parts:
- The left-side panel contains the entity's identifying information, collected from data sources like Azure Active Directory, Azure Monitor, Azure Security Center, and Microsoft Defender.

- The center panel shows a graphical and textual timeline of notable events related to the entity, such as alerts, bookmarks, and activities. Activities are aggregations of notable events from Log Analytics. The queries that detect those activities are developed by Microsoft security research teams.

- The right-side panel presents behavioral insights on the entity. These insights help to quickly identify anomalies and security threats. The insights are developed by Microsoft security research teams, and are based on anomaly detection models.

### The timeline

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Entity pages timeline":::

The timeline is a major part of the entity page's contribution to behavior analytics in Azure Sentinel. It presents a story about entity-related events, helping you understand the entity's activity within a specific time frame.

You can choose the **time range** from among several preset options (such as *last 24 hours*), or set it to any custom-defined time frame. Additionally, you can set filters that limit the information in the timeline to specific types of events or alerts.

The following types of items are included in the timeline:

- Alerts - any alerts in which the entity is defined as a **mapped entity**. Note that if your organization has created [custom alerts using analytics rules](./tutorial-detect-threats-custom.md), you should make sure that the rules' entity mapping is done properly.

- Bookmarks - any bookmarks that include the specific entity shown on the page.

- Activities - aggregation of notable events relating to the entity. 
 
### Entity Insights
 
Entity insights are queries defined by Microsoft security researchers to help your analysts investigate more efficiently and effectively. The insights are presented as part of the entity page, and provide valuable security information on hosts and users, in the form of tabular data and charts. Having the information here means you don't have to detour to Log Analytics. The insights include data regarding Sign-Ins, Group Additions, Anomalous Events and more, and include advanced ML algorithms to detect anomalous behavior. 
The insights are based on the following data types:
- Syslog
- SecurityEvent
- Audit Logs
- Sign-in Logs
- Office Activity
- BehaviorAnalytics (UEBA) 
 
### How to use entity pages

Entity pages are designed to be part of multiple usage scenarios, and can be accessed from incident management, the investigation graph, bookmarks, or directly from the entity search page under **Entity behavior analytics** in the Azure Sentinel main menu.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Entity page use cases":::


## Data schema

### Behavior analytics table

| Field                     | Description                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | unique ID number of the tenant                                      |
| SourceRecordId            | unique ID number of the EBA event                                   |
| TimeGenerated             | timestamp of the activity's occurrence                              |
| TimeProcessed             | timestamp of the activity's processing by the EBA engine            |
| ActivityType              | high-level category of the activity                                 |
| ActionType                | normalized name of the activity                                     |
| UserName                  | username of the user that initiated the activity                    |
| UserPrincipalName         | full username of the user that initiated the activity               |
| EventSource               | data source that provided the original event                        |
| SourceIPAddress           | IP address from which activity was initiated                        |
| SourceIPLocation          | country from which activity was initiated, enriched from IP address |
| SourceDevice              | hostname of the device that initiated the activity                  |
| DestinationIPAddress      | IP address of the target of the activity                            |
| DestinationIPLocation     | country of the target of the activity, enriched from IP address     |
| DestinationDevice         | name of the target device                                           |
| **UsersInsights**         | contextual enrichments of involved users                            |
| **DevicesInsights**       | contextual enrichments of involved devices                          |
| **ActivityInsights**      | contextual analysis of activity based on our profiling              |
| **InvestigationPriority** | anomaly score, between 0-10 (0=benign, 10=highly anomalous)         |
|

### Querying behavior analytics data

Using [KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/), we can query the Behavioral Analytics Table.

For example – if we want to find all the cases of a user that failed to sign in to an Azure resource, where it was the user's first attempt to connect from a given country, and connections from that country are uncommon even for the user's peers, we can use the following query:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### User peers metadata - table and notebook

User peers' metadata provides important context in threat detections, in investigating an incident, and in hunting for a potential threat. Security analysts can observe the normal activities of a user's peers to determine if the user's activities are unusual as compared to those of his or her peers.

Azure Sentinel calculates and ranks a user's peers, based on the user’s Azure AD security group membership, mailing list, et cetera, and stores the peers ranked 1-20 in the **UserPeerAnalytics** table. The screenshot below shows the schema of the UserPeerAnalytics table, and displays the top eight-ranked peers of the user Kendall Collins. Azure Sentinel uses the *term frequency-inverse document frequency* (TF-IDF) algorithm to normalize the weighing for calculating the rank: the smaller the group, the higher the weight. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Screen shot of user peers metadata table":::

You can use the [Jupyter notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) provided in the Azure Sentinel GitHub repository to visualize the user peers metadata. For detailed instructions on how to use the notebook, see the [Guided Analysis - User Security Metadata](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) notebook.

### Permission analytics - table and notebook

Permission analytics helps determine the potential impact of the compromising of an organizational asset by an attacker. This impact is also known as the asset's "blast radius." Security analysts can use this information to prioritize investigations and incident handling.

Azure Sentinel determines the direct and transitive access rights held by a given user to Azure resources, by evaluating the Azure subscriptions the user can access directly or via groups or service principals. This information, as well as the full list of the user's Azure AD security group membership, is then stored in the **UserAccessAnalytics** table. The screenshot below shows a sample row in the UserAccessAnalytics table, for the user Alex Johnson. **Source entity** is the user or service principal account, and **target entity** is the resource that the source entity has access to. The values of **access level** and **access type** depend on the access-control model of the target entity. You can see that Alex has Contributor access to the Azure subscription *Contoso Hotels Tenant*. The access control model of the subscription is RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Screen shot of user access analytics table":::

You can use the [Jupyter notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (the same notebook mentioned above) from the Azure Sentinel GitHub repository to visualize the permission analytics data. For detailed instructions on how to use the notebook, see the [Guided Analysis - User Security Metadata](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) notebook.

### Hunting queries and exploration queries

Azure Sentinel provides out-of-the-box a set of hunting queries, exploration queries, and a workbook, based on the BehaviorAnalytics table. These tools present enriched data, focused on specific use cases, that indicate anomalous behavior. 

Learn more about [hunting and the investigation graph](./hunting.md) in Azure Sentinel.

## Next steps
In this document, you learned about Azure Sentinel's entity behavior analytics capabilities. For practical guidance on implementation, and to use the insights you've gained, see the following articles:

- [Enable entity behavior analytics](./enable-entity-behavior-analytics.md) in Azure Sentinel.
- [Hunt for security threats](./hunting.md).