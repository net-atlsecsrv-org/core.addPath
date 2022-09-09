---
title: Azure N-series AMD GPU driver setup for Windows 
description: How to set up AMD GPU drivers for N-series VMs running Windows Server or Windows in Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha

---

# Install AMD GPU drivers on N-series VMs running Windows

To take advantage of the GPU capabilities of the new Azure NVv4 series VMs running Windows, AMD GPU drivers must be installed. The [AMD GPU Driver Extension](../extensions/hpccompute-amd-gpu-windows.md) installs AMD GPU drivers on a NVv4-series VM. Install or manage the extension using the Azure portal or tools such as Azure PowerShell or Azure Resource Manager templates. See the [AMD GPU Driver Extension documentation](../extensions/hpccompute-amd-gpu-windows.md) for supported operating systems and deployment steps.

If you choose to install AMD GPU drivers manually, this article provides supported operating systems, drivers, and installation and verification steps.

Only GPU drivers published by Microsoft are supported on NVv4 VMs. Please DO NOT install GPU drivers from any other source.

For basic specs, storage capacities, and disk details, see [GPU Windows VM sizes](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## Supported operating systems and drivers

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## Driver installation

1. Connect by Remote Desktop to each NVv4-series VM.

2. Download and install the latest driver.

3. Reboot the VM.

## Verify driver installation

You can verify driver installation in Device Manager. The following example shows successful configuration of the Radeon Instinct MI25 card on an Azure NVv4 VM.
<br />
![GPU driver properties](./media/n-series-amd-driver-setup/device-manager.png)

You can use dxdiag to verify the GPU display properties including the video RAM. The following example shows a 1/2 partition of the Radeon Instinct MI25 card on an Azure NVv4 VM.
<br />
![GPU driver properties](./media/n-series-amd-driver-setup/dxdiag-output.png)

If you are running Windows 10 build 1903 or higher then dxdiag will show no information in the 'Display' tab. Please use the 'Save All Information' option at the bottom and the output file will show the information related to AMD MI25 GPU.

![GPU driver properties](./media/n-series-amd-driver-setup/dxdiag-details.png)


