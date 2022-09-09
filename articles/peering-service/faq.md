---
title: Azure Peering Service Preview FAQ
description: Learn about Microsoft Azure Peering Service FAQs
services: peering-service
author: ypitsch
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 11/04/2019
ms.author: ypitsch
---

# Peering Service Preview FAQ

This article explains the most frequently asked questions about Azure Peering Service connections.

> [!IMPORTANT]
> Peering Service is currently in public preview.
> This preview version is provided without a service level agreement. We don't recommend it for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental terms of use for Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Q. Who are the target customers?**

A. Target customers are enterprises that connect to Microsoft cloud by using the internet as transport.

**Q. Can customers sign up for Peering Service with multiple providers?** 

A. Yes, customers can sign up for Peering Service with multiple providers in the same region or different regions, but not for the same prefix.

**Q. Can customers select a unique ISP for their sites per geographical region?**

A. Yes, customers can do so. Select the partner ISP per region that suits your business and operational needs.

**Q. What is Microsoft edge PoP?**

A. It's a physical location where Microsoft interconnects with other networks. In the Microsoft edge PoP location, services such as Azure Front Door and Azure CDN are hosted. For more information, see [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features).

## Peering Service: Unique characteristics

**Q. How is Peering Service different from normal internet access?**

A. Partners who have registered with Microsoft Peering Service are working with Microsoft to offer optimized latency and reliable connectivity to Microsoft services.  

**Q. How is Peering Service different from ExpressRoute?**

A. Azure ExpressRoute is a private, dedicated connection from the given one or multiple customer locations. While Peering Service offers optimized public connectivity and doesn't support any private connectivity, it also offers optimized connectivity for local internet breakouts.

## Next steps

- To learn about Peering Service, see [Peering Service overview](about.md).
- To find a service provider, see [Peering Service partners and locations](location-partners.md).
- To onboard a Peering Service connection, see [Onboarding Peering Service](onboarding-model.md).
- To register a Peering Service connection, see [Register a Peering Service connection - Azure portal](azure-portal.md).
- To measure telemetry, see [Measure connection telemetry](measure-connection-telemetry.md).