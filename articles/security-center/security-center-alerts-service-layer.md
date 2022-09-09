---
title: Threat detection for Azure service layer in Azure Security Center | Microsoft Docs
description: This topic presents the Azure service layer alerts available in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
---

# Threat detection for the Azure service layer in Azure Security Center

This topic presents the Azure Security Center alerts available when monitoring the following Azure service layers:

* [Azure network layer](#network-layer)
* [Azure management layer (Azure Resource Manager) (Preview)](#management-layer)

>[!NOTE]
>The following analytics are applicable to all resource types. They use the telemetry that Security Center provides by tapping into Azure internal feeds.

## Azure network layer<a name="network-layer"></a>

Security Center network-layer analytics are based on sample [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), which are packet headers collected by Azure core routers. Based on this data feed, Security Center machine learning models identify and flag malicious traffic activities. To enrich IP addresses, Security Center makes use of the Microsoft Threat Intelligence database.

> [!div class="mx-tableFixed"]

|Alert|Description|
|---|---|
|**Suspicious outgoing RDP network activity**|Sampled network traffic analysis detected anomalous outgoing Remote Desktop Protocol (RDP) communication, originating from a resource in your deployment. This activity is considered abnormal for this environment. It might indicate that your resource has been compromised, and is now being used to brute force attack an external RDP endpoint. Note that this type of activity might cause your IP to be flagged as malicious by external entities.|
|**Suspicious outgoing RDP network activity to multiple destinations**|Sampled network traffic analysis detected anomalous outgoing RDP communication, originating from a resource in your deployment to multiple destinations. This activity is considered abnormal for this environment. It might indicate that your resource has been compromised, and is now being used to brute force attack external RDP endpoints. Note that this type of activity might cause your IP to be flagged as malicious by external entities.|
|**Suspicious outgoing SSH network activity**|Sampled network traffic analysis detected anomalous outgoing Secure Shell (SSH) communication, originating from a resource in your deployment. This activity is considered abnormal for this environment. It might indicate that your resource has been compromised, and is now being used to brute force attack an external SSH endpoint. Note that this type of activity might cause your IP to be flagged as malicious by external entities.|
|**Suspicious outgoing SSH network activity to multiple destinations**|Sampled network traffic analysis detected anomalous outgoing SSH communication, originating from a resource in your deployment to multiple destinations. This activity is considered abnormal for this environment. It might indicate that your resource has been compromised, and is now being used to brute force attack external SSH endpoints. Note that this type of activity might cause your IP to be flagged as malicious by external entities.|
|**Suspicious incoming SSH network activity from multiple sources**|Sampled network traffic analysis detected anomalous incoming SSH communications from multiple sources to a resource in your deployment. Various unique IPs connecting to your resource is considered abnormal for this environment. This activity might indicate an attempt to brute force attack your SSH interface from multiple hosts (Botnet).|
|**Suspicious incoming SSH network activity**|Sampled network traffic analysis detected anomalous incoming SSH communication to a resource in your deployment. A relatively high number of incoming connections to your resource is considered abnormal for this environment. This activity might indicate an attempt to brute force attack your SSH interface.
|**Suspicious incoming RDP network activity from multiple sources**|Sampled network traffic analysis detected anomalous incoming RDP communications from multiple sources to a resource in your deployment. Various unique IPs connecting to your resource is considered abnormal for this environment. This activity might indicate an attempt to brute force attack your RDP interface from multiple hosts (Botnet).|
|**Suspicious incoming RDP network activity**|Sampled network traffic analysis detected anomalous incoming RDP communication to a resource in your deployment. A relatively high number of incoming connections to your resource is considered abnormal for this environment. This activity might indicate an attempt to brute force attack your SSH interface.|
|**Network communication with a malicious address has been detected**|Sampled network traffic analysis detected communication originating from a resource in your deployment with a possible command and control (C&C) server. Note that this type of activity could possibly cause your IP to be flagged as malicious by external entities.|

To understand how Security Center can use network related signals to apply threat protection, see [Heuristic DNS detections in Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Azure network layer threat detection alerts, in Azure Security Center, are only generated on virtual machines which have been assigned the same IP address for the entire hour during which a suspicious communication has taken place. This applies to virtual machines, as well as virtual machines that are created in the customer’s subscription as part of a managed service (e.g. AKS, Databricks).

## Azure management layer (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

>[!NOTE]
>Security Center protection layer based on Azure Resource Manager is currently in preview.

Security Center offers an additional layer of protection by using Azure Resource Manager events, which is considered to be the control plane for Azure. By analyzing the Azure Resource Manager records, Security Center detects unusual or potentially harmful operations in the Azure subscription environment.

> [!div class="mx-tableFixed"]

|Alert|Description|
|---|---|
|**MicroBurst toolkit run**|A known cloud-environment reconnaissance toolkit run has been detected in your environment. The tool [MicroBurst](https://github.com/NetSPI/MicroBurst) can be used by an attacker (or penetration tester) to map your subscriptions' resources, identify insecure configurations, and leak confidential information.|
|**Azurite toolkit run**|A known cloud-environment reconnaissance toolkit run has been detected in your environment. The tool [Azurite](https://github.com/mwrlabs/Azurite) can be used by an attacker (or penetration tester) to map your subscriptions' resources and identify insecure configurations.|
|**Suspicious management session using an inactive account**|Subscription activity logs analysis has detected suspicious behavior. A principal not in use for a long period of time is now performing actions that can secure persistence for an attacker.|
|**Suspicious management session using PowerShell**|Subscription activity logs analysis has detected suspicious behavior. A principal that doesn’t regularly use PowerShell to manage the subscription environment is now using PowerShell, and performing actions that can secure persistence for an attacker.|
|**Use of advanced Azure persistence techniques**|Subscription activity logs analysis has detected suspicious behavior. Customized roles have been given legitimized identity entities. This can lead the attacker to gain persistency in an Azure customer environment.|
|**Activity from infrequent country**|Activity from a location that wasn't recently or ever visited by any user in the organization has occurred.<br/>This detection considers past activity locations to determine new and infrequent locations. The anomaly detection engine stores information about previous locations used by users in the organization. 
|**Activity from anonymous IP addresses**|Users activity from an IP address that has been identified as an anonymous proxy IP address has been detected. <br/>These proxies are used by people who want to hide their device’s IP address, and can be used for malicious intent. This detection uses a machine learning algorithm that reduces false positives, such as mis-tagged IP addresses that are widely used by users in the organization.|
|**Impossible travel detected**|Two user activities (in a single or multiple sessions) have occurred, originating from geographically distant locations. This occurs within a time period shorter than the time it would have taken the user to travel from the first location to the second. This indicates that a different user is using the same credentials. <br/>This detection uses a machine learning algorithm that ignores obvious false positives contributing to the impossible travel conditions, such as VPNs and locations regularly used by other users in the organization. The detection has an initial learning period of seven days, during which it learns a new user’s activity pattern.|

>[!NOTE]
> Several of the preceding analytics are powered by Microsoft Cloud App Security. To benefit from these analytics, you must activate a Cloud App Security license. If you have a Cloud App Security license, then these alerts are enabled by default. To disable them:
>
> 1. In the **Security Center** blade, select **Security policy**. For the subscription you want to change, select **Edit settings**.
> 2. Select **Threat detection**.
> 3. Under **Enable integrations**, clear **Allow Microsoft Cloud App Security to access my data**, and select **Save**.

>[!NOTE]
>Security Center stores security-related customer data in the same geo as its resource. If Microsoft hasn't yet deployed Security Center in the resource's geo, then it stores the data in the United States. When Cloud App Security is enabled, this information is stored in accordance with the geo location rules of Cloud App Security. For more information, see [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/).
