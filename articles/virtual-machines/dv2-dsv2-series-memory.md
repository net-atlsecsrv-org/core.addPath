---
title: Memory optimized Dv2 and DSv2-series VMs - Azure Virtual Machines
description: Specifications for the Dv2 and DSv2-series VMs.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
---

# Memory optimized Dv2 and Dsv2-series

Dv2 and Dsv2-series, a follow-on to the original D-series, features a more powerful CPU. DSv2-series sizes run on Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), or the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors. The Dv2-series has the same memory and disk configurations as the D-series.

## Dv2-series 11-15

Dv2-series sizes run on Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), or the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors.

ACU: 210 - 250

Premium Storage: Not Supported

Premium Storage caching: Not Supported

Live Migration: Supported

Memory Preserving Updates: Supported

| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | Max temp storage throughput: IOPS/Read MBps/Write MBps | Max data disks/throughput: IOPS | Max NICs|Expected network bandwidth (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> Instance is isolated to hardware dedicated to a single customer.
<sup>2</sup> 25000 Mbps with Accelerated Networking.

## DSv2-series 11-15

DSv2-series sizes run on Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), or the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors.

ACU: 210 - 250 <sup>1</sup>

Premium Storage:  Supported

Premium Storage caching:  Supported

Live Migration: Supported

Memory Preserving Updates: Supported

| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | Max data disks | Max cached and temp storage throughput: IOPS/MBps (cache size in GiB) | Max uncached disk throughput: IOPS/MBps | Max NICs|Expected network bandwidth (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> The maximum disk throughput (IOPS or MBps) possible with a DSv2 series VM may be limited by the number, size and striping of the attached disk(s).  For details, see [Designing for high performance](./premium-storage-performance.md).
<sup>2</sup>  Instance is isolated to the Intel Haswell based hardware and dedicated to a single customer.  
<sup>3</sup> Constrained core sizes available.  
<sup>4</sup> 25000 Mbps with Accelerated Networking.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## Other sizes and information

- [General purpose](sizes-general.md)
- [Memory optimized](sizes-memory.md)
- [Storage optimized](sizes-storage.md)
- [GPU optimized](sizes-gpu.md)
- [High performance compute](sizes-hpc.md)
- [Previous generations](sizes-previous-gen.md)

Pricing Calculator: [Pricing Calculator](https://azure.microsoft.com/pricing/calculator/)

More information on Disks Types : [Disk Types](./disks-types.md#ultra-disk)


## Next steps

Learn more about how [Azure compute units (ACU)](acu.md) can help you compare compute performance across Azure SKUs.