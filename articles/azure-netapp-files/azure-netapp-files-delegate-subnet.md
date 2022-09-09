---
title: Delegate a subnet to Azure NetApp Files | Microsoft Docs
description: Learn how to delegate a subnet to Azure NetApp Files. Specify the delegated subnet when you create a volume.
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
ms.date: 09/28/2020
ms.author: b-juche
---
# Delegate a subnet to Azure NetApp Files 

You must delegate a subnet to Azure NetApp Files.   When you create a volume, you need to specify the delegated subnet.

## Considerations

* The wizard for creating a new subnet defaults to a /24 network mask, which provides for 251 available IP addresses. Using a /28 network mask, which provides for 11 usable IP addresses, is sufficient for the service.
* In each Azure Virtual Network (VNet), only one subnet can be delegated to Azure NetApp Files.   
   Azure enables you to create multiple delegated subnets in a VNet.  However, any attempts to create a new volume will fail if you use more than one delegated subnet.  
   You can have only a single delegated subnet in a VNet. A NetApp account can deploy volumes into multiple VNets, each having its own delegated subnet.  
* You cannot designate a network security group or service endpoint in the delegated subnet. Doing so causes the subnet delegation to fail.
* Access to a volume from a globally peered virtual network is not currently supported.
* [User-defined routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (UDRs) and Network security groups (NSGs) are not supported on delegated subnets for Azure NetApp Files. However, you can apply UDRs and NSGs to other subnets, even within the same VNet as the subnet delegated to Azure NetApp Files.  
   Azure NetApp Files creates a system route to the delegated subnet. The route is shown in **Effective routes** in the route table if you need it for troubleshooting.

## Steps

1.	Go to the **Virtual networks** blade in the Azure portal and select the virtual network that you want to use for Azure NetApp Files.    

1. Select **Subnets** from the Virtual network blade and click the **+Subnet** button. 

1. Create a new subnet to use for Azure NetApp Files by completing the following required fields in the Add Subnet page:
    * **Name**: Specify the subnet name.
    * **Address range**: Specify the IP address range.
    * **Subnet delegation**: Select **Microsoft.NetApp/volumes**. 

      ![Subnet delegation](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
You can also create and delegate a subnet when you [create a volume for Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## Next steps

* [Create a volume for Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Learn about virtual network integration for Azure services](../virtual-network/virtual-network-for-azure-services.md)