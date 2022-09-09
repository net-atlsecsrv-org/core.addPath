---
title: High-performance computing on InfiniBand enabled H-series and N-series VMs - Azure Virtual Machines
description: Learn about the features and capabilities of InfiniBand enabled H-series and N-series VMs optimized for HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms-subservice: hpc
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/29/2020
---

# High-performance computing on InfiniBand enabled H-series and N-series VMs

Azure's InfiniBand enabled H-series and N-series VMs are designed to deliver leadership-class performance, Message Passing Interface (MPI) scalability, and cost efficiency for a variety of real-world HPC and AI workloads. These high-performance computing (HPC) optimized VMs are used to solve some of the most computationally intensive problems in science and engineering such as: fluid dynamics, earth modeling, weather simulations, etc.

These articles describe how to get started on the InfiniBand-enabled H-series and N-series VMs on Azure as well as optimal configuration of the HPC and AI workloads on the VMs for scalability.

## Features and capabilities

The InfiniBand enabled H-series and N-series VMs are designed to provide the best HPC performance, MPI scalability, and cost efficiency for HPC workloads. See [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs to learn more about the features and capabilities of the VMs.

### RDMA and InfiniBand

[RDMA capable](../../sizes-hpc.md#rdma-capable-instances) [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs communicate over the low latency and high bandwidth InfiniBand network. The RDMA capability over such an interconnect is critical to boost the scalability and performance of distributed-node HPC and AI workloads. The InfiniBand enabled H-series and N-series VMs are connected in a non-blocking fat tree with a low-diameter design for optimized and consistent RDMA performance.
See [Enable InfiniBand](enable-infiniband.md) to learn more about setting up InfiniBand on the InfiniBand enabled VMs.

### Message passing interface

The SR-IOV enabled H-series and N-series support almost all MPI libraries and versions. Some of the most common, supported MPI libraries are: Intel MPI, OpenMPI, MPICH, MVAPICH2, Platform MPI, and all remote direct memory access (RDMA) verbs.
See [Set up MPI](setup-mpi.md) to learn more about installing various supported MPI libraries and their optimal configuration.

## Get started

The first step is to select the [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VM type optimal for the workload based on the VM specifications and [RDMA capability](../../sizes-hpc.md#rdma-capable-instances).
Second, configure the VM by enabling InfiniBand. There are various methods to doing this including using optimized VM images with drivers baked-in; see [Optimization for Linux](configure.md) and [Enable InfiniBand](enable-infiniband.md) for details.
Third, for distributed node workloads, choosing and configuring MPI is critical. See [Set up MPI](setup-mpi.md) for details.
Fourth, for performance and scalability, optimally configure the workloads by following guidance specific to the VM family, such as for [HB-series overview](hb-series-overview.md) and [HC-series overview](hc-series-overview.md).

## Next steps

- Learn about [configuring and optimizing](configure.md) the InfiniBand enabled [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs.
- Review the [HB-series overview](hb-series-overview.md) and [HC-series overview](hc-series-overview.md) to learn about optimally configuring workloads for performance and scalability.
- Read about the latest announcements and some HPC examples and results at the [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- For a higher level architectural view of running HPC workloads, see [High Performance Computing (HPC) on Azure](/azure/architecture/topics/high-performance-computing/).
