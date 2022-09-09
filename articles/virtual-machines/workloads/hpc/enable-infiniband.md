---
title: Enable InifinBand on HPC VMs - Azure Virtual Machines | Microsoft Docs
description: Learn how to enable InfiniBand on Azure HPC VMs. 
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn

---

# Enable InfiniBand

[RDMA capable](../../sizes-hpc.md#rdma-capable-instances) [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs communicate over the low latency and high bandwidth InfiniBand network. The RDMA capability over such an interconnect is critical to boost the scalability and performance of distributed-node HPC and AI workloads. The InfiniBand enabled H-series and N-series VMs are connected in a non-blocking fat tree with a low-diameter design for optimized and consistent RDMA performance.

There are various ways to enable InfiniBand on the capable VM sizes.

## VM Images with InfiniBand drivers
See [VM Images](configure.md#vm-images) for a list of supported VM Images on the Marketplace, which come pre-loaded with InfiniBand drivers (for SR-IOV or non-SR-IOV VMs) or can be configured with the appropriate drivers.
For SR-IOV enabled [RDMA capable VMs](../../sizes-hpc.md#rdma-capable-instances), [CentOS-HPC version 7.6 or a later](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) version VM images in the Marketplace are the easiest way to get started.
The Ubuntu VM images can be configured with the right drivers for both SR-IOV and non-SR-IOV enabled VMs using the [instructions here](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).

## InfiniBand Driver VM Extensions
On Linux, the [InfiniBandDriverLinux VM extension](../../extensions/hpc-compute-infiniband-linux.md) can be used to install the Mellanox OFED drivers and enable InfiniBand on the SR-IOV enabled H- and N-series VMs.

On Windows, the [InfiniBandDriverWindows VM extension](../../extensions/hpc-compute-infiniband-windows.md) installs Windows Network Direct drivers (on non-SR-IOV VMs) or Mellanox OFED drivers (on SR-IOV VMs) for RDMA connectivity. In certain deployments of A8 and A9 instances, the HpcVmDrivers extension is added automatically. Note that the HpcVmDrivers VM extension is being deprecated; it will not be updated.

To add the VM extension to a VM, you can use [Azure PowerShell](/powershell/azure/) cmdlets. For more information, see [Virtual machine extensions and features](../../extensions/overview.md). You can also work with extensions for VMs deployed in the [classic deployment model](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

## Manual installation
[Mellanox OpenFabrics drivers (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) can be manually installed on the [SR-IOV enabled](../../sizes-hpc.md#rdma-capable-instances) [H-series](../../sizes-hpc.md) and [N-series](../../sizes-gpu.md) VMs.

### Linux
The [OFED drivers for Linux](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) can be installed with the example below. Though the example here is for RHEL/CentOS, but the steps are general and can be used for any compatible Linux operating system such as Ubuntu (16.04, 18.04 19.04, 20.04) and SLES (12 SP4 and 15). More examples for others distros is on the [azhpc-images repo](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). The inbox drivers also work as well, but the Mellanox OFED drivers provide more features.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### Windows
For Windows, download and install the [Mellanox OFED for Windows drivers](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## Enable IP over InfiniBand (IB)
If you are plan to run MPI jobs, you typically don't need IPoIB. The MPI library will use the verbs interface for IB communication (unless you explicitly use the TCP/IP channel of MPI library). But if you have an app that uses TCP/IP for communication and you want to run over IB, you can use IPoIB over the IB interface. Use the following commands (for RHEL/CentOS) to enable IP over InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## Next steps

- Learn more about installing various [supported MPI libraries](setup-mpi.md) and their optimal configuration on the VMs.
- Review the [HB-series overview](hb-series-overview.md) and [HC-series overview](hc-series-overview.md) to learn about optimally configuring workloads for performance and scalability.
- Read about the latest announcements and some HPC examples and results at the [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- For a higher level architectural view of running HPC workloads, see [High Performance Computing (HPC) on Azure](/azure/architecture/topics/high-performance-computing/).
