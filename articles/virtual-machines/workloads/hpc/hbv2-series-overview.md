--- 
title: HBv2-series VM overview - Azure Virtual Machines | Microsoft Docs 
description: Learn about the HBv2-series VM size in Azure.  
services: virtual-machines 
author: vermagit 
tags: azure-resource-manager 
ms.service: virtual-machines 
ms.subservice: workloads
ms.workload: infrastructure-services 
ms.topic: article 
ms.date: 09/28/2020 
ms.author: amverma 
ms.reviewer: cynthn
--- 

 
# HBv2 series virtual machine overview 

 
Maximizing high performance compute (HPC) application performance on AMD EPYC requires a thoughtful approach memory locality and process placement. Below we outline the AMD EPYC architecture and our implementation of it on Azure for HPC applications. We will use the term **pNUMA** to refer to a physical NUMA domain, and **vNUMA** to refer to a virtualized NUMA domain. 

Physically, an [HBv2-series](../../hbv2-series.md) server is 2 * 64-core EPYC 7742 CPUs for a total of 128 physical cores. These 128 cores are divided into 32 pNUMA domains (16 per socket), each of which is 4 cores and termed by AMD as a **Core Complex** (or **CCX**). Each CCX has its own L3 cache, which is how an OS will see a pNUMA/vNUMA boundary. Four adjacent CCXs share access to 2 channels of physical DRAM. 

To provide room for the Azure hypervisor to operate without interfering with the VM, we reserve physical pNUMA domains 0 and 16 (i.e. the first CCX of each CPU socket). All remaining 30 pNUMA domains are assigned to the VM at which point they become vNUMA. Thus, the VM will see:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` cores per VM 

The VM itself has no awareness that pNUMA 0 and 16 are reserved. It enumerates the vNUMA it sees as 0-29, with 15 vNUMA per socket symmetrically, vNUMA 0-14 on vSocket 0, and vNUMA 15-29 on vSocket 1. In the next section, there are instructions on how best to run MPI applications on this asymmetric NUMA layout. 

Process pinning will work on HBv2-series VMs because we expose the underlying silicon as-is to the guest VM. We strongly recommend process pinning for optimal performance and consistency. 


## Hardware specifications 

| Hardware Specifications          | HBv2-series VM                   | 
|----------------------------------|----------------------------------|
| Cores                            | 120 (SMT disabled)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU Frequency (non-AVX)          | ~3.1 GHz (single + all cores)    | 
| Memory                           | 4 GB/core (480 GB total)         | 
| Local Disk                       | 960 GB NVMe (block), 480 GB SSD (page file) | 
| Infiniband                       | 200 Gb/s EDR Mellanox ConnectX-6 | 
| Network                          | 50 Gb/s Ethernet (40 Gb/s usable) Azure second Gen SmartNIC | 


## Software specifications 

| Software Specifications     | HBv2-series VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| Max MPI Job Size            | 36000 cores (300 VMs in a single virtual machine scale set with singlePlacementGroup=true) |
| MPI Support                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Additional Frameworks       | Unified Communication X, libfabric, PGAS                  |
| Azure Storage Support       | Standard and Premium Disks (maximum 8 disks)              |
| OS Support for SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+           |
| Orchestrator Support        | CycleCloud, Batch                                         | 


## Next steps

- Learn more about [AMD EPYC architecture](https://bit.ly/2Epv3kC) and [multi-chip architectures](https://bit.ly/2GpQIMb). For more detailed information, see the [HPC Tuning Guide for AMD EPYC Processors](https://bit.ly/2T3AWZ9).
- Read about the latest announcements and some HPC examples in the [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- For a higher level architectural view of running HPC workloads, see [High Performance Computing (HPC) on Azure](/azure/architecture/topics/high-performance-computing/).