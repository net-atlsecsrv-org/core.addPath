---
title: Retrieve the current POP IP list for Azure CDN| Microsoft Docs
description: Learn how to retrieve the current POP list.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: 

---
# Retrieve the current POP IP list for Azure CDN

## Retrieve the current Verizon POP IP list for Azure CDN

You can use the REST API to retrieve the set of IPs for Verizon’s point of presence (POP) servers. These POP servers  make requests to origin servers that are associated with Azure Content Delivery Network (CDN) endpoints on a Verizon profile (**Azure CDN Standard from Verizon** or **Azure CDN Premium from Verizon**). Note that this set of IPs is different from the IPs that a client would see when making requests to the POPs. 

For the syntax of the REST API operation for retrieving the POP list, see [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## Retrieve the current Microsoft POP IP list for Azure CDN

To lock down your application to accept traffic only from Azure CDN from Microsoft, you will need to set up IP ACLs for your backend. You may also restrict the set of accepted values for the header 'X-Forwarded-Host' sent by Azure CDN from Microsoft. These steps are detailed out as below:

Configure IP ACLing for your backends to accept traffic from Azure CDN from Microsoft's backend IP address space and Azure's infrastructure services only. 

* Azure CDN from Microsoft's IPv4 backend IP space: 147.243.0.0/16
* Azure CDN from Microsoft's IPv6 backend IP space: 2a01:111:2050::/44

IP Ranges and Service tags for Microsoft services can be found [here](https://www.microsoft.com/download/details.aspx?id=56519)

Filter on the values for the incoming header 'X-Forwarded-Host' sent by Azure CDN from Microsoft. The only allowed values for the header should be all of the endpoint hosts as defined in your CDN config. In fact even more specifically, only the host names for which you want to accept traffic from, on this particular origin of yours.

## Typical use case

For security purposes, you can use this IP list to enforce that requests to your origin server are made only from a valid Verizon POP. For example, if someone discovered the hostname or IP address for a CDN endpoint's origin server, one could make requests directly to the origin server, therefore bypassing the scaling and security capabilities provided by Azure CDN. By setting the IPs in the returned list as the only allowed IPs on an origin server, this scenario can be prevented. To ensure that you have the latest POP list, retrieve it at least once a day. 

## Next steps

For information about the REST API, see [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
