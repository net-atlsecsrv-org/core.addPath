---
title: Connect Stream Analytics jobs to resources in an Azure Virtual Network (VNET)
description: This article describes how to connect an Azure Stream Analytics job with resources that are in a VNET.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/23/2020
ms.custom: devx-track-js
---
# Connect Stream Analytics jobs to resources in an Azure Virtual Network (VNet)

Your Stream Analytics jobs make outbound connections to your input and output Azure resources to process data in real time and produce results. These input and output resources (for example, Azure Event Hubs and Azure SQL Database) could be behind an Azure firewall or in an Azure Virtual Network (VNet). Stream Analytics service operates from networks that can't be directly included in your network rules.

However, there are two ways to securely connect your Stream Analytics jobs to your input and output resources in such scenarios.
* Using private endpoints in Stream Analytics clusters.
* Using Managed Identity authentication mode coupled with 'Allow trusted services' networking setting.

Your Stream Analytics job does not accept any inbound connection.

## Private endpoints in Stream Analytics clusters.
[Stream Analytics clusters](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) is a single tenant dedicated compute cluster where you can run your Stream Analytics jobs. You can create managed private endpoints in your Stream Analytics cluster, which allows any jobs running on your cluster to make a secure outbound connection to your input and output resources.

The creation of private endpoints in your Stream Analytics cluster is a [two step operation](https://docs.microsoft.com/azure/stream-analytics/private-endpoints). This option is best suited for medium to large streaming workloads as the minimum size of a Stream Analytics cluster is 36 SUs (although the 36 SUs can be shared by different jobs in various subscriptions or environments like development, test, and production).

## Managed identity authentication with 'Allow trusted services' configuration
Some Azure services provide **Allow trusted Microsoft services** networking setting, which when enabled, allows your Stream Analytics jobs to securely connect to your resource using strong authentication. This option allows you to connect your jobs to your input and output resources without requiring a Stream Analytics cluster and private endpoints. Configuring your job to use this technique is a 2-step operation:
* Use Managed Identity authentication mode when configuring input or output in your Stream Analytics job.
* Grant your specific Stream Analytics jobs explicit access to your target resources by assigning an Azure role to the job's system-assigned managed identity. 

Enabling **Allow trusted Microsoft services** does not grant blanket access to any job. This gives you full control of which specific Stream Analytics jobs can access your resources securely. 

Your jobs can connect to the following Azure services using this technique:
1. [Blob Storage or Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) - can be your job's storage account, streaming input or output.
2. [Azure Event Hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) - can be your job's streaming input or output.

If your jobs need to connect to other input or output types, then the only option is to use private endpoints in Stream Analytics clusters.

## Next steps

* [Create and remove Private Endpoints in Stream Analytics clusters](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Connect to Event Hubs in a VNet using Managed Identity authentication](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Connect to Blob storage and ADLS Gen2 in a VNet using Managed Identity authentication](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
