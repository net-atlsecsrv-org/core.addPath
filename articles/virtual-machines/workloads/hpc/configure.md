---
title: Configuration and Optimization of InfiniBand enabled H-series and N-series Azure Virtual Machines
description: Learn about configuring and optimizing the InfiniBand enabled H-series and N-series VMs for HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn

---

# Configure and optimize VMs

This article shares some guidance on configuring and optimizing the InfiniBand enabled [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs for HPC.

## VM images
On InfiniBand (IB) enabled VMs, the appropriate drivers are required to enable RDMA.
- The [CentOS-HPC VM images](#centos-hpc-vm-images) in the Marketplace come pre-configured with the appropriate IB drivers.
- The [Ubuntu-HPC VM images](#ubuntu-hpc-vm-images) in the Marketplace come pre-configured with the appropriate IB drivers and GPU drivers.

These VM images (VMI) are based on the base CentOS and Ubuntu marketplace VM images. Scripts used in the creation of these VM images from their base CentOS Marketplace image are on the [azhpc-images repo](https://github.com/Azure/azhpc-images/tree/master/centos).

On GPU enabled [N-series](../../sizes-gpu.md) VMs, the appropriate GPU drivers are additionally required. This can be available by the following methods:
- Use the [Ubuntu-HPC VM images](#ubuntu-hpc-vm-images) which come pre-configured with the Nvidia GPU drivers and GPU compute software stack (CUDA, NCCL).
- Add the GPU drivers through the [VM extensions](../../extensions/hpccompute-gpu-linux.md)
- Install the GPU drivers [manually](../../linux/n-series-driver-setup.md).
- Some other VM images on the Marketplace also come pre-installed with the Nvidia GPU drivers, including some VM images from Nvidia.

Depending on the workloads' Linux distro and version needs, both the [CentOS-HPC VM images](#centos-hpc-vm-images) and [Ubuntu-HPC VM images](#ubuntu-hpc-vm-images) in the Marketplace are the easiest way to get started with HPC and AI workloads on Azure.
It is also recommended to create [custom VM images](../../linux/tutorial-custom-images.md) with workload specific customization and configuration and reuse those recurringly.

### VM sizes supported by the HPC VM images
The latest Azure HPC marketplace images come with Mellanox OFED 5.1 and above, which do not support ConnectX3-Pro InfiniBand cards. These VM images only support ConnextX-5 and newer InfiniBand cards. This implies the following VM size support matrix for the InfiniBand OFED in these HPC VM images:
- [H-series](../../sizes-hpc.md): HB, HC, HBv2, HBv3
- [N-series](../../sizes-gpu.md): NDv2, NDv4

Note that for GPU support in the N-series - NDv2 and NDv4 VM sizes, currently only the [Ubuntu-HPC VM images](#ubuntu-hpc-vm-images) come pre-configured with the Nvidia GPU drivers and GPU compute software stack (CUDA, NCCL). 

Also note that all the above VM sizes support "Gen 2" VMs, though some older ones also support "Gen 1" VMs.

### CentOS-HPC VM images

#### SR-IOV enabled VMs
For SR-IOV enabled [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances), CentOS-HPC VM images version 7.6 and later are suitable. These VM images come optimized and pre-loaded with the Mellanox OFED drivers for RDMA and various commonly used MPI libraries and scientific computing packages.
- The available or latest versions of the VM images can be listed with the following information using [CLI](/cli/azure/vm/image#az_vm_image_list) or [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview).
   ```bash
   "publisher": "OpenLogic",
   "offer": "CentOS-HPC",
   ```
- Scripts used in the creation of the CentOS-HPC version 7.6 and later VM images from a base CentOS Marketplace image are on the [azhpc-images repo](https://github.com/Azure/azhpc-images/tree/master/centos).
- Additionally, details on what's included in the CentOS-HPC version 7.6 and later VM images, and how to deploy them are in a [TechCommunity article](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

> [!NOTE] 
> SR-IOV enabled N-series VM sizes with FDR InfiniBand (e.g. NCv3 and older) will be able to use the following CentOS-HPC VM image or older versions from the Marketplace:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### Non SR-IOV enabled VMs
For non-SR-IOV enabled [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances), CentOS-HPC version 6.5 or a later version, up to 7.4 in the Marketplace are suitable. As an example, for [H16-series VMs](../../h-series.md), versions 7.1 to 7.4 are recommended. These VM images come pre-loaded with the Network Direct drivers for RDMA and Intel MPI version 5.1.

> [!NOTE]
> On these CentOS-based HPC images for non-SR-IOV enabled VMs, kernel updates are disabled in the **yum** configuration file. This is because the NetworkDirect Linux RDMA drivers are distributed as an RPM package, and driver updates might not work if the kernel is updated.

### Ubuntu-HPC VM images
For SR-IOV enabled [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances), Ubuntu-HPC VM images version 18.04 are suitable. These VM images come optimized and pre-loaded with the Mellanox OFED drivers for RDMA, Nvidia GPU drivers, GPU compute software stack (CUDA, NCCL), and various commonly used MPI libraries and scientific computing packages.
- The available or latest versions of the VM images can be listed with the following information using [CLI](/cli/azure/vm/image#az_vm_image_list) or [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-hpc?tab=overview).
   ```bash
   "publisher": "Microsoft-DSVM",
   "offer": "Ubuntu-HPC",
   ```
- Scripts used in the creation of the Ubuntu-HPC VM images from a base Ubuntu Marketplace image are on the [azhpc-images repo](https://github.com/Azure/azhpc-images/tree/master/ubuntu).
- Additionally, details on what's included in the Ubuntu-HPC VM images, and how to deploy them are in a [TechCommunity article](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

### RHEL/CentOS VM images
The base RHEL or CentOS-based non-HPC VM images on the Marketplace can be configured for use on the SR-IOV enabled [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances). Learn more about [enabling InfiniBand](enable-infiniband.md) and [setting up MPI](setup-mpi.md) on the VMs.
- Scripts used in the creation of the CentOS-HPC version 7.6 and later VM images from a base CentOS Marketplace image from the [azhpc-images repo](https://github.com/Azure/azhpc-images/tree/master/centos) can also be used.
 
### Ubuntu VM images
The base Ubuntu Server 16.04 LTS, 18.04 LTS, and 20.04 LTS VM images in the Marketplace are supported for both SR-IOV and non-SR-IOV [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances). Learn more about [enabling InfiniBand](enable-infiniband.md) and [setting up MPI](setup-mpi.md) on the VMs.
- Instructions for enabling InfiniBand on the Ubuntu VM images are in a [TechCommunity article](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Scripts used in the creation of the Ubuntu 18.04 and 20.04 LTS based HPC VM images from a base Ubuntu Marketplace image are on the [azhpc-images repo](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

> [!NOTE]
> Mellanox OFED 5.1 and above do not support ConnectX3-Pro InfiniBand cards on SR-IOV enabled N-series VM sizes with FDR InfiniBand (e.g. NCv3). Please use LTS Mellanox OFED version 4.9-0.1.7.0 or older on the N-series VM's with ConnectX3-Pro cards. Please see more details [here](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### SUSE Linux Enterprise Server VM images
SLES 12 SP3 for HPC, SLES 12 SP3 for HPC (Premium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (Premium), SLES 12 SP4 and SLES 15 VM images in the Marketplace are supported. These VM images come pre-loaded with the Network Direct drivers for RDMA (on the non-SR-IOV VM sizes) and Intel MPI version 5.1. Learn more about [setting up MPI](setup-mpi.md) on the VMs.

## Optimize VMs

The following are some optional optimization settings for improved performance on the VM.

### Update LIS

If necessary for functionality or performance, [Linux Integration Services (LIS) drivers](../../linux/endorsed-distros.md) can be installed or updated on supported OS distros, especially is deploying using a custom image or an older OS version such as CentOS/RHEL 6.x or earlier version of 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### Reclaim memory

Improve performance by automatically reclaiming memory to avoid remote memory access.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

To make this persist after VM reboots:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### Disable firewall and SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### Disable cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### Configure WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Optionally, the WALinuxAgent may be disabled as a pre-job step and enabled back post-job for maximum VM resource availability to the HPC workload.


## Next steps

- Learn more about [enabling InfiniBand](enable-infiniband.md) on the InfiniBand-enabled [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs.
- Learn more about installing and running various [supported MPI libraries](setup-mpi.md) on the VMs.
- Review the [HBv3-series overview](hbv3-series-overview.md) and [HC-series overview](hc-series-overview.md).
- Read about the latest announcements, HPC workload examples, and performance results at the [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- For a higher level architectural view of running HPC workloads, see [High Performance Computing (HPC) on Azure](/azure/architecture/topics/high-performance-computing/).
