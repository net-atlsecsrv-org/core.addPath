---
title: Retrieve load balancer information by using the Azure Instance Metadata Service
titleSuffix: Azure Load Balancer
description: Get started learning about using the Azure Instance Metadata Service to retrieve load balancer information.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: allensu

---
# Retrieve load balancer information by using the Azure Instance Metadata Service

IMDS (Azure Instance Metadata Service) provides information about currently running virtual machine instances. The service is a REST API that's available at a well-known, non-routable IP address (169.254.169.254). 

When you place virtual machine or virtual machine set instances behind an Azure Standard Load Balancer, use the IMDS to retrieve metadata related to the load balancer and the instances.

The metadata includes the following information for the virtual machines or virtual machine scale sets:

* Standard SKU public IP.
* Inbound rule configurations of the load balancer of each private IP of the network interface.
* Outbound rule configurations of the load balancer of each private IP of the network interface.

## Access the load balancer metadata using the IMDS

For more information on how to access the load balancer metadata, see [Use the Azure Instance Metadata Service to access load balancer information](howto-load-balancer-imds.md).

## Troubleshoot common error codes

For more information on common error codes and their mitigation methods, see [Troubleshoot common error codes when using IMDS](troubleshoot-load-balancer-imds.md). 

## Support

If you're unable to retrieve a metadata response after multiple attempts, create a support issue in the Azure portal.

## Next steps
Learn more about [Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

[Deploy a standard load balancer](quickstart-load-balancer-standard-public-portal.md)

