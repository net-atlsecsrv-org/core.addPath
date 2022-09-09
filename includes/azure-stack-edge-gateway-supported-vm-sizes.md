---
author: alkohli
ms.service: databox  
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
---

The VM size determines the amount of compute resources (like CPU, GPU, and memory) that are made available to the VM. You should create virtual machines by using a VM size appropriate for the workload. Even though all machines will be running on the same hardware, machine sizes have different limits for disk access. This can help you manage overall disk access across your VMs. If a workload increases, you can also resize an existing virtual machine.

The following VMs are supported for creation on your Azure Stack Edge device.

### Dv2-series
|Size     |vCPU     |Memory (GiB) | Temp storage (GiB)   | Max data disks | Max NICs |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3.5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### DSv2-series
|Size     |vCPU     |Memory (GiB) | Temp storage (GiB)  | Max data disks| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3.5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


For more information, see [Dv2 and DSv2-series](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### NCasT4_v3-series (Preview)

These sizes are supported for GPU VMs on your device and are optimized for compute-intensive GPU-accelerated applications. This series is focused on inference workloads featuring Nvidia's Tesla T4 GPU. 

|Size     |vCPU     |Memory (GiB) | Temp storage (GiB)  | GPU | GPU memory (GiB) | Max NICs |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

For more information, see [NCasT4_v3-series](../articles/virtual-machines/nct4-v3-series.md).

### F-series

These series are optimized for computational workloads and run on Intel Xeon processors. 

| Size | vCPU's | Memory: GiB | Temp storage (GiB) |  Max data disks | Max NICs |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

For more information, see [Fsv2-series](../articles/virtual-machines/fsv2-series.md).

