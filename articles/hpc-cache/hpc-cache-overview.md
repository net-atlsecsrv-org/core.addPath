---
title: Azure HPC Cache overview
description: Describes Azure HPC Cache, a file access accelerator solution for high-performance computing 
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 11/23/2020
ms.author: v-erkel
ms.custom: references_regions
---

# What is Azure HPC Cache?

Azure HPC Cache speeds access to your data for high-performance computing (HPC) tasks. By caching files in Azure, Azure HPC Cache brings the scalability of cloud computing to your existing workflow. This service can be used even for workflows where your data is stored across WAN links, such as in your local datacenter network-attached storage (NAS) environment.

Azure HPC Cache is easy to launch and monitor from the Azure portal. Existing NFS storage or new Blob containers can become part of its aggregated namespace, which makes client access simple even if you change the back-end storage target.

## Overview video

[![video thumbnail: Azure HPC Cache overview - click to visit video page](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Click the image above to watch a [short overview of Azure HPC Cache](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## Use cases

Azure HPC Cache enhances productivity best for workflows like these:

* Read-heavy file access workflow
* Data stored in NFS-accessible storage, Azure Blob, or both
* Compute farms of up to 75,000 CPU cores

Azure HPC Cache can be added to a wide variety of workflows across many industries. Any system where a large number of machines need to access a set of files at scale and with low latency will benefit from this service. The sections below give specific examples.

### Visual effects (VFX) rendering

In media and entertainment, Azure HPC Cache can speed up data access for time-critical rendering projects. VFX rendering workflows often require last-minute processing by large numbers of compute nodes. Data for these workflows are typically located in an on-premises NAS environment. Azure HPC Cache can cache that file data in the cloud to reduce latency and enhance flexibility for on-demand rendering.

### Life sciences

Many life sciences workflows can benefit from scale-out file caching.

A research institute that wants to port its genomic analysis workflows into Azure can easily shift them by using Azure HPC Cache. Because the cache provides POSIX file access, no client-side changes are needed to run their existing client workflow in the cloud.

Azure HPC Cache also can be leveraged to improve efficiency in tasks like secondary analysis, pharmacological simulation, or AI-driven image analysis.

### Financial services analytics

An Azure HPC Cache deployment can help speed up quantitative analysis calculations, risk analysis workloads, and Monte Carlo simulations to give financial services companies better insight to make strategic decisions.

## Region availability

Visit the [Azure Global Infrastructure products by region](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) page to learn where Azure HPC Cache is available.

The [customer-managed keys feature](customer-keys.md) is supported only in these Azure regions:

* East US
* South Central US
* West US 2
* West Europe
* US Gov Arizona
* US Gov Virginia

Azure HPC Cache resides in a single region. It can access data stored in other regions if you connect it to Blob containers located there. The cache does not permanently store customer data.

## Next steps

* Read the [Azure HPC Cache product page](https://azure.microsoft.com/services/hpc-cache) to learn more about its capabilities
* Learn about product [prerequisites](hpc-cache-prerequisites.md)
* [Create an Azure HPC Cache](hpc-cache-create.md) from the Azure portal
