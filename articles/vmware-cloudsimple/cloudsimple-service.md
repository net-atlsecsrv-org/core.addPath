--- 
title: VMware Solutions by CloudSimple - Service 
description: Describes the overview of CloudSimple service and concepts. 
author: sharaths-cs 
ms.author: b-shsury 
ms.date: 4/24/19 
ms.topic: article 
ms.service: vmware 
ms.reviewer: cynthn 
manager: dikamath 
---
# CloudSimple Service overview

The CloudSimple service allows you to consume Azure VMware Solution by CloudSimple.  Creating the service allows you to purchase nodes, reserve nodes, and create Private Clouds.  You create the CloudSimple service in each Azure region where the CloudSimple service is available.  The service defines the edge network of Azure VMware Solution by CloudSimple.  The edge network supports services that include VPN, ExpressRoute, and internet connectivity to your Private Clouds.

## Gateway subnet

A gateway subnet is required per CloudSimple service and is unique to the region in which it's created. The gateway subnet is used when creating the edge network and requires a /28 CIDR block.  The gateway subnet address space must be unique. It must not overlap with any network that communicates with the CloudSimple environment. The networks that communicate with CloudSimple include on-premises networks and Azure virtual network.  A gateway subnet can't be deleted once it's created.  The gateway subnet is removed when the service is deleted.

## Next steps

* Learn how to [Create a CloudSimple service on Azure](quickstart-create-cloudsimple-service.md)