---
title: Avere vFXT for Azure
description: Introduction to Avere vFXT for Azure, a cloud cache layer for HPC
author: ekpgh
ms.service: avere-vfxt
ms.topic: overview
ms.date: 12/03/2019
ms.author: rohogue
---

# What is Avere vFXT for Azure?

Avere vFXT for Azure is a filesystem caching solution for data-intensive high-performance computing (HPC) tasks. It lets you take advantage of cloud computing's scalability to make your data accessible when and where it’s needed - even for data that’s stored in your own on-premises hardware.

Avere vFXT supports these common computing scenarios:

* Hybrid cloud architecture - Avere vFXT for Azure can work with a hardware storage system, which provides the benefit of cloud computing without having to move files.

* Cloud bursting - Avere vFXT for Azure can help you move your data to the cloud for a single project, or "lift and shift" the entire workflow permanently.

![diagram showing details of the Avere vFXT system inside an Azure subscription connected to Blob storage and to an on-premises datacenter](media/avere-vfxt-hybrid.png)

Avere vFXT for Azure is best suited for these situations:

* Read-heavy operations for HPC workloads
* Applications using the common NFS protocol
* Compute farms of 1000 to 40,000 CPU cores
* Integration with on-premises hardware NAS, Azure Blob storage, or both

For more information, visit <https://azure.microsoft.com/services/storage/avere-vfxt/>

## Who uses Avere vFXT for Azure?

Avere vFXT can help with all sorts of read-intensive computing tasks:

### Visual effects rendering

In media and entertainment, the Avere vFXT cluster can speed up data access for time-critical rendering projects. Because you can add more cache space as well as more compute nodes in Azure, you have the flexibility to handle large projects efficiently.

### Life sciences

Avere vFXT lets researchers run secondary analysis workflows in Azure Compute, and access genomic data no matter their location.

In pharmaceutical research, Avere vFXT clusters can expedite drug discovery by helping researchers predict drug-target interactions and analyze research data.

### Financial services analytics

An Avere vFXT cluster can help speed up quantitative analysis calculations, which gives financial services companies better insight to make strategic decisions.

## Features and specifications

The Avere vFXT system is made up of three or more virtual edge filer nodes, configured in a cluster. It can be situated near the client machines, which mount the cluster instead of mounting the storage directly.

The Avere vFXT cluster caches files as they are requested. Repeated requests can be served from the cache more than 80% of the time.

### Compatibility

* Compatible with hardware NAS systems from NetApp or Dell EMC Isilon
* Compatible with Azure Blob
* Uses NFSv3 or SMB2 protocol

Avere vFXT for Azure uses the following Azure resources:

|Azure component|   |
|----------|-----------|
|Virtual machines|3 or more E32s_v3|
|Premium SSD storage|200 GB OS space plus 1 TB to 4 TB cache space per node |
|Storage account (optional) |v2|
|Data backend storage (optional) | One empty LRS Blob container |

## Next steps

Read these articles to plan and create your own Avere vFXT for Azure deployment.

* [Plan your system](avere-vfxt-deploy-plan.md)
* [Deployment overview](avere-vfxt-deploy-overview.md)
* [Create the vFXT](avere-vfxt-deploy.md)
