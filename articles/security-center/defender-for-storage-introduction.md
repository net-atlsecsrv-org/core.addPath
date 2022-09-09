---
title: Azure Defender for Storage - the benefits and features 
description: Learn about the benefits and features of Azure Defender for Storage.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin

---

# Introduction to Azure Defender for Storage


**Azure Defender for Storage** is an Azure-native layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit your storage accounts. It utilizes the advanced capabilities of security AI and [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) to provide contextual security alerts and recommendations.

Security alerts are triggered when anomalies in activity occur. These  alerts are integrated with Azure Security Center, and are also sent via email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats.

## Availability

|Aspect|Details|
|----|:----|
|Release state:|Generally available (GA)|
|Pricing:|**Azure Defender for Storage** is billed as shown on [the pricing page](security-center-pricing.md)|
|Protected storage types:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Clouds:|![Yes](./media/icons/yes-icon.png) Commercial clouds<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) China Gov, Other Gov|
|||


## What are the benefits of Azure Defender for Storage?

Azure Defender for Storage provides:

- **Azure-native security** - With 1-click enablement, Defender for Storage protects data stored in Azure Blob, Azure Files, and Data Lakes. As an Azure-native service, Defender for Storage provides centralized security across all data assets managed by Azure and is integrated with other Azure security services such as Azure Sentinel.
- **Rich detection suite** - Powered by Microsoft Threat Intelligence, the detections in Defender for Storage cover the top storage threats such as anonymous access, compromised credentials, social engineering, privilege abuse, and malicious content.
- **Response at scale** - Security Center's automation tools make it easier to prevent and respond to identified threats. Learn more in [Automate responses to Security Center triggers](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="High-level overview of the features of Azure Defender for Storage":::


## What kind of alerts does Azure Defender for Storage provide?

Security alerts are triggered when there's:

- **Suspicious access patterns** - such as successful access from a Tor exit node or from an IP considered suspicious by Microsoft Threat Intelligence
- **Suspicious activities** - such as anomalous data extraction or unusual change of access permissions
- **Upload of malicious content** - such as potential malware files (based on hash reputation analysis) or hosting of phishing content

Alerts include details of the incident that triggered them, as well as recommendations on how to investigate and remediate threats. Alerts can be exported to Azure Sentinel or any other third-party SIEM or any other external tool.

> [!TIP]
> It's a best practice to [configure Azure Defender for Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center) on the subscription level, but you may also [configure it on individual storage accounts](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal).


## What is hash reputation analysis for malware?

To determine whether an uploaded file is suspicious, Azure Defender for Storage uses hash reputation analysis supported by [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). The threat protection tools don’t scan the uploaded files, rather they examine the storage logs and compare the hashes of newly uploaded files with those of known viruses, trojans, spyware, and ransomware. 

When a file is suspected to contain malware, Security Center displays an alert and can optionally email the storage owner for approval to delete the suspicious file. To set up this automatic removal of files that hash reputation analysis indicates contain malware, deploy a [workflow automation to trigger on alerts that contain "Potential malware uploaded to a storage account”](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> To enable Security Center's threat protection capabilities, you must enable Azure Defender on the subscription containing the applicable workloads.
>
> You can enable **Azure Defender for Storage** at either the subscription level or resource level.



## Next steps

In this article, you learned about Azure Defender for Storage.

For related material, see the following articles: 

- Whether an alert is generated by Security Center, or received by Security Center from a different security product, you can export it. To export your alerts to Azure Sentinel, any third-party SIEM, or any other external tool, follow the instructions in [Exporting alerts to a SIEM](continuous-export.md).
- [How to enable Advanced Defender for Storage](../storage/common/azure-defender-storage-configure.md)
- [The list of Azure Defender for Storage alerts](alerts-reference.md#alerts-azurestorage)
- [Microsoft's threat intelligence capabilities](https://go.microsoft.com/fwlink/?linkid=2128684)
