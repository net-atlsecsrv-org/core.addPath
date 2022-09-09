---
title: HC-series VM overview - Azure Virtual Machines| Microsoft Docs
description: Learn about the preview support for the HC-series VM size in Azure. 
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
---

# HC-series virtual machine overview

Maximizing HPC application performance on Intel Xeon Scalable Processors requires a thoughtful approach to process placement on this new architecture. Here, we outline our implementation of it on Azure HC-series VMs for HPC applications. We will use the term “pNUMA” to refer to a physical NUMA domain, and “vNUMA” to refer to a virtualized NUMA domain. Similarly, we will use the term “pCore” to refer to physical CPU cores, and “vCore” to refer to virtualized CPU cores.

Physically, an [HC-series](../../hc-series.md) server is 2 * 24-core Intel Xeon Platinum 8168 CPUs for a total of 48 physical cores. Each CPU is a single pNUMA domain, and has unified access to six channels of DRAM. Intel Xeon Platinum CPUs feature a 4x larger L2 cache than in prior generations (256 KB/core -> 1 MB/core), while also reducing the L3 cache compared to prior Intel CPUs (2.5 MB/core -> 1.375 MB/core).

The above topology carries over to the HC-series hypervisor configuration as well. To provide room for the Azure hypervisor to operate without interfering with the VM, we reserve pCores 0-1 and 24-25 (that is, the first 2 pCores on each socket). We then assign pNUMA domains all remaining cores to the VM. Thus, the VM will see:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` cores per VM

The VM has no knowledge that pCores 0-1 and 24-25 weren't given to it. Thus, it exposes each vNUMA as if it natively had 22 cores.

Intel Xeon Platinum, Gold, and Silver CPUs also introduce an on-die 2D mesh network for communication within and external to the CPU socket. We strongly recommend process pinning for optimal performance and consistency. Process pinning will work on HC-series VMs because the underlying silicon is exposed as-is to the guest VM.

The following diagram shows the segregation of cores reserved for Azure Hypervisor and the HC-series VM.

![Segregation of cores reserved for Azure Hypervisor and HC-series VM](./media/hc-series-overview/segregation-cores.png)

## Hardware specifications

| Hardware Specifications          | HC-series VM                     |
|----------------------------------|----------------------------------|
| Cores                            | 44 (HT disabled)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| CPU Frequency (non-AVX)          | 3.7 GHz (single core), 2.7-3.4 GHz (all cores) |
| Memory                           | 8 GB/core (352 total)            |
| Local Disk                       | 700 GB SSD                       |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5   |
| Network                          | 50 Gb Ethernet (40 Gb usable) Azure second Gen SmartNIC    |

## Software specifications

| Software Specifications     |HC-series VM           |
|-----------------------------|-----------------------|
| Max MPI Job Size            | 13200 cores (300 VMs in a single virtual machine scale set with singlePlacementGroup=true)  |
| MPI Support                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Additional Frameworks       | Unified Communication X, libfabric, PGAS |
| Azure Storage Support       | Standard and Premium Disks (maximum 4 disks) |
| OS Support for SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Orchestrator Support        | CycleCloud, Batch  |

## Next steps

- Learn more about [Intel Xeon SP architecture](https://bit.ly/2RCYkiE).
- Read about the latest announcements and some HPC examples and results at the [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- For a higher level architectural view of running HPC workloads, see [High Performance Computing (HPC) on Azure](/azure/architecture/topics/high-performance-computing/).
