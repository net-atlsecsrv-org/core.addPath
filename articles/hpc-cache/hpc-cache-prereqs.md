---
title: Azure HPC Cache prerequisites
description: Prerequisites for using Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
---

# Prerequisites for Azure HPC Cache

Before using the Azure portal to create a new Azure HPC Cache, make sure your environment meets these requirements.

## Azure subscription

A paid subscription is recommended.

> [!NOTE]
> During the first several months of the GA release, the Azure HPC Cache team must add your subscription to the access list before it can be used to create a cache instance. This procedure helps ensure that each customer gets high-quality responsiveness from their caches. Fill out [this form](https://aka.ms/onboard-hpc-cache) to request access.

## Network infrastructure

Two network-related prerequisites should be set up before you can use your cache:

* A dedicated subnet for the Azure HPC Cache instance
* DNS support so that the cache can access storage and other resources

### Cache subnet

The Azure HPC Cache needs a dedicated subnet with these qualities:

* The subnet must have at least 64 IP addresses available.
* The subnet cannot host any other VMs, even for related services like client machines.
* If you use multiple Azure HPC Cache instances, each one needs its own subnet.

The best practice is to create a new subnet for each cache. You can create a new virtual network and subnet as part of creating the cache.

### DNS access

The cache needs DNS to access resources outside of its virtual network. Depending on which resources you are using, you might need to set up a customized DNS server and configure forwarding between that server and Azure DNS servers:

* To access Azure Blob storage endpoints and other internal resources, you need the Azure-based DNS server.
* To access on-premises storage, you need to configure a custom DNS server that can resolve your storage hostnames.

If you only need access to Blob storage, you can use the default Azure-provided DNS server for your cache. However, if you need access to other resources, you should create a custom DNS server and configure it to forward any Azure-specific resolution requests to the Azure DNS server.

A simple DNS server also can be used to load balance client connections among all the available cache mount points.

Learn more about Azure virtual networks and DNS server configurations in [Name resolution for resources in Azure virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## Permissions

Check these permission-related prerequisites before starting to create your cache.

* The cache instance needs to be able to create virtual network interfaces (NICs). The user who creates the cache must have sufficient privileges in the subscription to create NICs.

* If using Blob storage, Azure HPC Cache needs authorization to access your storage account. Use role-based access control (RBAC) to give the cache access to your Blob storage. Two roles are required: Storage Account Contributor and Storage Blob Data Contributor.

  Follow the instructions in [Add storage targets](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) to add the roles.

## Storage infrastructure

The cache supports Azure Blob containers or NFS hardware storage exports. Add storage targets after you create the cache.

Each storage type has specific prerequisites.

### NFS storage requirements

If using on-premises hardware storage, the cache needs to have high-bandwidth network access to the datacenter from its subnet. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) or similar access is recommended.

NFS back-end storage must be a compatible hardware/software platform. Contact the Azure HPC Cache team for details.

### Blob storage requirements

If you want to use Azure Blob storage with your cache, you need a compatible storage account and either an empty Blob container or a container that is populated with Azure HPC Cache formatted data as described in [Move data to Azure Blob storage](hpc-cache-ingest.md).

Create the account and container before attempting to add it as a storage target.

To create a compatible storage account, use these settings:

* Performance: **Standard**
* Account kind: **StorageV2 (general purpose v2)**
* Replication: **Locally redundant storage (LRS)**
* Access tier (default): **Hot**

It's a good practice to use a storage account in the same location as your cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

You also must give the cache application access to your Azure storage account as mentioned in [Permissions](#permissions), above. Follow the procedure in [Add storage targets](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) to give the cache the required access roles. If you are not the storage account owner, have the owner do this step.

## Next steps

* [Create an Azure HPC Cache instance](hpc-cache-create.md) from the Azure portal
