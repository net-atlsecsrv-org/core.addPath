---
title: Metrics for Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files provides metrics on allocated storage, actual storage usage, volume IOPS, and latency. Use these metrics to understand usage and performance.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: b-juche
---
# Metrics for Azure NetApp Files

Azure NetApp Files provides metrics on allocated storage, actual storage usage, volume IOPS, and latency. By analyzing these metrics, you can gain a better understanding on the usage pattern and volume performance of your NetApp accounts.  

## <a name="capacity_pools"></a>Usage metrics for capacity pools

- *Pool Allocated Size*   
    The provisioned size of the pool.

- *Pool Allocated to Volume Size*  
    The total of volume quota (GiB) in a given capacity pool (that is, the total of the volumes' provisioned sizes in the capacity pool).  
    This size is the size you selected during volume creation.  

- *Pool Consumed Size*  
    The total of logical space (GiB) used across volumes in a capacity pool.  

- *Total Snapshot Size of the Pool*    
    The sum of the snapshot size of all volumes in the pool.

## <a name="volumes"></a>Usage metrics for volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Volume Consumed Size*   
    The total logical space used in a volume (GiB).  
    This size includes logical space used by active file systems and snapshots.  
- *Volume Snapshot Size*   
   The incremental logical space used by snapshots in a volume.  

## Performance metrics for volumes

- *Average Read Latency*   
    The average time for reads from the volume in milliseconds.
- *Average Write Latency*   
    The average time for writes from the volume in milliseconds.
- *Read IOPS*   
    The number of reads to the volume per second.
- *Write IOPS*   
    The number of writes to the volume per second.

## <a name="replication"></a>Volume replication metrics

- *Is volume replication status healthy*   
    The condition of the replication relationship. 

- *Is volume replication transferring*    
    Whether the status of the volume replication is ‘transferring’. 
 
- *Volume replication lag time*   
    The amount of time in seconds by which the data on the mirror lags behind the source. 

- *Volume replication last transfer duration*   
    The amount of time in seconds it took for the last transfer to complete. 

- *Volume replication last transfer size*    
    The total number of bytes transferred as part of the last transfer. 

- *Volume replication progress*    
    The total amount of data transferred for the current transfer operation. 

- *Volume replication total transfer*   
    The cumulative bytes transferred for the relationship. 

## Next steps

* [Understand the storage hierarchy of Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Set up a capacity pool](azure-netapp-files-set-up-capacity-pool.md)
* [Create a volume for Azure NetApp Files](azure-netapp-files-create-volumes.md)
