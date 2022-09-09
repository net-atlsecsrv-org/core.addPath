---
title: Manage your account- Azure Batch | Microsoft Docs
description: Learn what comprises an Azure Batch account 
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''

ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017

---

# Manage your Batch account

A Batch account is a uniquely identified entity within the Batch service. All processing is associated with a Batch account.

You can create an Azure Batch account using the [Azure portal](batch-account-create-portal.md) or programmatically, such as with the [Batch Management .NET library](batch-management-dotnet.md). When creating the account, you can associate an Azure storage account for storing job-related input and output data or applications.

You can run multiple Batch workloads in a single Batch account, or distribute your workloads among Batch accounts that are in the same subscription, but in different Azure regions.

## Components of the Batch account

The Batch account enables you to run large-scale parallel and high-performance computing (HPC) batch jobs efficiently in Azure. Within the account you manage:

- the applications you are running

- the allocation of pools and nodes within pools

- the number and types of tasks 

- the input and output of data. You don't need to install additional software to manage tasks.

- When you create the Batch account, you are asked to assign a name to it. This name is its ID and once assigned cannot be changed.

- To change the name of an account, you need to delete it and create a new Batch account.

- The account is created within the subscription you want to use.

- Use the account to identify and retrieve primary and secondary account keys from any Batch account within your subscription.

- The account maintains information about pool allocation and core quotas.  

- The account contains location information.

- The account identifies your storage account.

## Next steps

- Create a Batch account using the [Azure portal](batch-account-create-portal.md).
- Create a Batch account programmatically, such as with the [Batch Management .NET library](batch-management-dotnet.md).
- [Configure or disable remote access to compute nodes in an Azure Batch pool](pool-endpoint-configuration.md).
- [Run job preparation and job release tasks on Batch compute nodes](batch-job-prep-release.md)
