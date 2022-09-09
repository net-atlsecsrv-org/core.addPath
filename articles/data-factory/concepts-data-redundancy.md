---
title: Data redundancy in Azure Data Factory | Microsoft Docs
description: 'Learn about meta-data redundancy mechanisms in Azure Data Factory'
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na

ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
---

# **Azure Data Factory data redundancy**

Azure Data Factory data includes metadata (pipeline, datasets, linked services, integration runtime and triggers) and monitoring data (pipeline, trigger, and activity runs). 

In all regions (except Brazil South and Southeast Asia), Azure Data Factory data is stored and replicated in the [paired region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#azure-regional-pairs) to protect against metadata loss. During regional datacenter failures, Microsoft may initiate a regional failover of your Azure Data Factory instance. In most cases, no action is required on your part. When the Microsoft-managed failover has completed, you will be able to access your Azure Data Factory in the failover region. 

Due to data residency requirements in Brazil South, and Southeast Asia, Azure Data Factory data is stored on [local region only](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage). For Southeast Asia, all the data are stored in Singapore. For Brazil South, all data are stored in Brazil. When the region is lost due to a significant disaster, Microsoft will not be able to recover your Azure Data Factory data.  

> [!NOTE]
> Microsoft-managed failover does not apply to self-hosted integration runtime (SHIR) since this infrastructure is typically customer-managed. If the SHIR is set up on Azure VM, then the recommendation is to leverage [Azure site recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) for handling the [Azure VM failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture) to another region.



## **Using source control in Azure Data Factory**

To ensure that you are able to track and audit the changes made to your Azure data factory metadata, you should consider setting up source control for your Azure Data Factory. It will also enable you to access your metadata JSON files for pipelines, datasets, linked services, and trigger. Azure Data Factory enables you to work with different Git repository (Azure DevOps and GitHub). 

 Learn how to set up [source control in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control). 

> [!NOTE]
> In case of a disaster (loss of region), new data factory can be provisioned manually or in an automated fashion. Once the new data factory has been created, you can restore your pipelines, datasets and linked services JSON from the existing Git repository. 



## **Data stores**

Azure Data Factory enables you to move data among data stores located on-premises and in the cloud. To ensure business continuity with your data stores, you should refer to the business continuity recommendations for each of these data stores. 

 

## See also

- [Azure Regional Pairs](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Data residency in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/) 
