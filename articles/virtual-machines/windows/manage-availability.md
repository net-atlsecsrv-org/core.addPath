---
title: Manage the availability of Windows VMs in Azure 
description: Learn how to use multiple virtual machines to ensure high availability for your Windows application in Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows

ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017

---
# Manage the availability of Windows virtual machines in Azure 

Learn ways to set up and manage multiple virtual machines to ensure high availability for your Windows application in Azure. You can also [manage the availability of Linux virtual machines](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

For instructions on creating and using availability sets when using the classic deployment model, see [How to Configure an Availability Set](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## Next steps
To learn more about load balancing your virtual machines, see [Load Balancing virtual machines](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

View Reference Architectures for running N-tier applications on SQL Server in IaaS

* [Windows N-tier application on Azure with SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Run an N-tier application in multiple Azure regions for high availability](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
