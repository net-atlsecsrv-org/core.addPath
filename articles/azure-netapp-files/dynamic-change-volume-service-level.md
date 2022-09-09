---
title: Dynamically change the service level of a volume for Azure NetApp Files  | Microsoft Docs
description: Describes how to dynamically change the service level of a volume.
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
ms.topic: how-to
ms.date: 07/24/2020
ms.author: b-juche
---
# Dynamically change the service level of a volume

You can change the service level of an existing volume by moving the volume to another capacity pool that uses the [service level](azure-netapp-files-service-levels.md) you want for the volume. This in-place service-level change for the volume does not require that you migrate data. It also does not impact access to the volume.  

> [!IMPORTANT] 
> Using this feature requires whitelisting. Email anffeedback@microsoft.com with your subscription ID to request this feature.

This functionality enables you to meet your workload needs on demand.  You can change an existing volume to use a higher service level for better performance, or to use a lower service level for cost optimization. For example, if the volume is currently in a capacity pool that uses the *Standard* service level and you want the volume to use the *Premium* service level, you can move the volume dynamically to a capacity pool that uses the *Premium* service level.  

The capacity pool that you want to move the volume to must already exist. The capacity pool can contain other volumes.  If you want to move the volume to a brand-new capacity pool, you need to [create the capacity pool](azure-netapp-files-set-up-capacity-pool.md) before you move the volume.  

## Considerations

* After the volume is moved to another capacity pool, you will no longer have access to the previous volume activity logs and volume metrics. The volume will start with new activity logs and metrics under the new capacity pool.

* If you move a volume to a capacity pool of a higher service level (for example, moving from *Standard* to *Premium* or *Ultra* service level), you must wait at least seven days before you can move the volume to a capacity pool of a lower service level again (for example, moving from *Ultra* to *Premium* or *Standard*).  
This wait period does not apply if you move the volume to a capacity pool that has the same service level or a lower service level.

## Steps

1.	On the Volumes page, right-click the volume whose service level you want to change. Select **Change Pool**.

    ![Right-click volume](../media/azure-netapp-files/right-click-volume.png)

2. In the Change pool window, select the capacity pool you want to move the volume to. 

    ![Change pool](../media/azure-netapp-files/change-pool.png)

3.	Click **OK**.


## Next steps  

* [Service levels for Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Set up a capacity pool](azure-netapp-files-set-up-capacity-pool.md)
