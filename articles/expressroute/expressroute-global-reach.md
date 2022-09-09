---
title: 'Azure ExpressRoute: Connect to Microsoft Cloud using Global Reach'
description: Learn how Azure ExpressRoute Global Reach can link ExpressRoute circuits together to make a private network between your on-premises networks.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: duau


---


# ExpressRoute Global Reach
ExpressRoute is a private and resilient way to connect your on-premises networks to the Microsoft Cloud. You can access many Microsoft cloud services such as Azure and Microsoft 365 from your private data center or your corporate network. For example, you may have a branch office in San Francisco with an ExpressRoute circuit in Silicon Valley and another branch office in London with an ExpressRoute circuit in the same city. Both branch offices have high-speed connectivity to Azure resources in US West and UK South. However, the branch offices cannot connect and send data directly with one another. In other words, 10.0.1.0/24 can send data to 10.0.3.0/24 and 10.0.4.0/24 network, but NOT to 10.0.2.0/24 network.

![Diagram that shows circuits not linked together with Express Route Global Reach.][1]

With **ExpressRoute Global Reach**, you can link ExpressRoute circuits together to make a private network between your on-premises networks. In the above example, with the addition of ExpressRoute Global Reach, your San Francisco office (10.0.1.0/24) can directly exchange data with your London office (10.0.2.0/24) through the existing ExpressRoute circuits and via Microsoft's global network. 

![Diagram that shows circuits linked together with Express Route Global Reach.][2]

## Use case
ExpressRoute Global Reach is designed to complement your service provider’s WAN implementation and connect your branch offices across the world. For example, if your service provider primarily operates in the United States and has linked all of your branches in the U.S., but the service provider doesn’t operate in Japan and Hong Kong, with ExpressRoute Global Reach you can work with a local service provider and Microsoft will connect your branches there to the ones in the U.S. using ExpressRoute and our global network.

![Diagram that shows a use case for Express Route Global Reach.][3]

## Availability 
ExpressRoute Global Reach is supported in most regions where ExpressRoute is currently supported. You can refer to [ExpressRoute connectivity providers](expressroute-locations-providers.md#partners) for current supported regions. 

> [!NOTE] 
> To enable ExpressRoute Global Reach between [different geopolitical regions](expressroute-locations-providers.md#locations), your circuits must be **Premium SKU**.

## Next steps
- View the [Global Reach FAQ](expressroute-faqs.md#globalreach).
- Learn how to [enable Global Reach](expressroute-howto-set-global-reach.md).
- Learn how to [link an ExpressRoute circuit to your virtual network](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagram without global reach"
[2]: ./media/expressroute-global-reach/2.png "diagram with global reach"
[3]: ./media/expressroute-global-reach/3.png "use case of global reach"
