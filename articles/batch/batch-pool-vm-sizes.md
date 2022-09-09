---
title: Choose VM sizes for pools - Azure Batch | Microsoft Docs
description: How to choose from the available VM sizes for compute nodes in Azure Batch pools
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''

ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: lahugh
ms.custom: seodec18

---
# Choose a VM size for compute nodes in an Azure Batch pool

When you select a node size for an Azure Batch pool, you can choose from among almost all the VM sizes available in Azure. Azure offers a range of sizes for Linux and Windows VMs for different workloads.

There are a few exceptions and limitations to choosing a VM size:

* Some VM families or VM sizes are not supported in Batch. 
* Some VM sizes are restricted and need to be specifically enabled before they can be allocated.

## Supported VM families and sizes

### Pools in Virtual Machine configuration

Batch pools in the Virtual Machine configuration support all VM sizes ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *except* for the following:

| Family  | Unsupported sizes  |
|---------|---------|
| Basic A-series | Basic_A0 (A0) |
| A-series | Standard_A0 |
| B-series | All |
| DC-series | All |
| Extreme memory optimized | All |
| Hb-series<sup>1,2</sup> | All |
| Hc-series<sup>1,2</sup> | All |
| Lsv2-series | All |
| NDv2-series<sup>1,2</sup> | All |
| NVv2-series<sup>1</sup> | All |
| SAP HANA | All |


<sup>1</sup> Planned for support.  
<sup>2</sup> Can be used by Batch accounts in user subscription mode; the user subscription mode Batch account needs to have the core quota set. See [configuration for user subscription mode](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) for more information.

The following VM sizes are supported only for low-priority nodes:

| Family  | Supported sizes  |
|---------|---------|
| M-series | Standard_M64ms |
| M-series | Standard_M128s |

Other VM sizes in the M-series family are currently unsupported.

### Pools in Cloud Service configuration

Batch pools in the Cloud Service configuration support all [VM sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md) *except* for the following:

| Family  | Unsupported sizes  |
|---------|---------|
| A-series | ExtraSmall |
| Av2-series | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## Restricted VM families

The following VM families can be allocated in Batch pools, but you must request a specific quota increase (see [this article](batch-quota-limit.md#increase-a-quota)):

* NCv2-series
* NCv3-series
* ND-series

These sizes can only be used in pools in the Virtual Machine configuration.

## Size considerations

* **Application requirements** - Consider the characteristics and requirements of the application you'll run on the nodes. Aspects like whether the application is multithreaded and how much memory it consumes can help determine the most suitable and cost-effective node size. For multi-instance [MPI workloads](batch-mpi.md) or CUDA applications, consider specialized [HPC](../virtual-machines/linux/sizes-hpc.md) or [GPU-enabled](../virtual-machines/linux/sizes-gpu.md) VM sizes, respectively. (See [Use RDMA-capable or GPU-enabled instances in Batch pools](batch-pool-compute-intensive-sizes.md).)

* **Tasks per node** - It's typical to select a node size assuming one task runs on a node at a time. However, it might be advantageous to have multiple tasks (and therefore multiple application instances) [run in parallel](batch-parallel-node-tasks.md) on compute nodes during job execution. In this case, it is common to choose a multicore node size to accommodate the increased demand of parallel task execution.

* **Load levels for different tasks** - All of the nodes in a pool are the same size. If you intend to run applications with differing system requirements and/or load levels, we recommend that you use separate pools. 

* **Region availability** - A VM family or size might not be available in the regions where you create your Batch accounts. To check that a size is available, see [Products available by region](https://azure.microsoft.com/regions/services/).

* **Quotas** - The [cores quotas](batch-quota-limit.md#resource-quotas) in your Batch account can limit the number of nodes of a given size you can add to a Batch pool. To request a quota increase, see [this article](batch-quota-limit.md#increase-a-quota). 

* **Pool configuration** - In general, you have more VM size options when you create a pool in the Virtual Machine configuration, compared with the Cloud Service configuration.

## Next steps

* For an in-depth overview of Batch, see [Develop large-scale parallel compute solutions with Batch](batch-api-basics.md).
* For information about using compute-intensive VM sizes, see [Use RDMA-capable or GPU-enabled instances in Batch pools](batch-pool-compute-intensive-sizes.md).
