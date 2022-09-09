---
title: Choose the right pricing tier for Azure Maps | Microsoft Docs
description: Learn about pricing tiers offered by Azure Maps 
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: 
---

# Choose the right pricing tier in Azure Maps

Azure Maps offers two pricing tiers. The purpose of this article is to help you choose the right pricing tier for your needs. To help choose the right pricing tier, ask yourself the following two questions.

## What geospatial capabilities do I plan to use?
The S0 pricing tier is right for you if the core geospatial APIs meet your service requirements. If you want more advanced capabilities for your application, consider opting for the S1 pricing tier. Example capabilities are areal plus hybrid imagery, getting route range, and batch geocoding. The **pricing tier capabilities** table that follows gives you a better idea of your application's needs. It also helps you choose a pricing tier most suitable for your application.

## How many concurrent users do I plan to support? 
The S0 and S1 pricing tiers handle different amounts of data throughput. Before you choose an Azure Maps pricing tier, ask yourself some questions. An example is "how many concurrent users do I want to support?" The S0 pricing tier handles up to **50 queries per second**. The S1 pricing tier handles **more than 50 queries per second**.

### Pricing tier capabilities

| Capability                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search (fwd/rev geocoding, points of interest)  |        ✓           |     ✓    |
| Batch geocoding (preview)              |                   |     ✓    |
| Polygons from search          |                   |     ✓    |
| Routing                                 |        ✓           |     ✓    |
| Route range                    |                   |     ✓    |
| Batch routing (preview)                |                   |     ✓    |
| Matrix routing (preview)               |                   |     ✓    |
| Render                                  |        ✓           |     ✓    |
| Imagery plus hybrid imagery    |            |     ✓    |
| Traffic                                 |        ✓           |     ✓    |
| Time zones                              |        ✓           |     ✓    |
| Geolocation (preview)                |        ✓           |     ✓    |
| Data  (preview)               |                   |     ✓    |
| Spatial  (preview)               |                   |     ✓    |
| Geofencing  (preview)               |                   |     ✓    |



These additional data points are worth considering:
* What kind of enterprise do you have?
* How critical is the application being built?

See the **pricing tier targeted customers** table to get a better sense of the S0 and S1 pricing tiers. For more information, see [Azure Maps pricing](https://azure.microsoft.com/pricing/details/azure-maps/). 

### Pricing tier targeted customers

| Pricing tier  |     Targeted customers                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>The S0 pricing tier is for customers who are either small or medium-sized enterprises. It's the right pricing tier for you if you don't expect high volumes of concurrent users. It's also right if the core geospatial APIs shown in the preceding table meet your service requirements. This tier is generally available. It works for applications in all stages of production: from proof-of-concept development and early stage testing to application production and deployment.<p>|
| S1            |    <p>The S1 pricing tier is for customers in need of support for large-scale enterprise, mission-critical applications, or high volumes of concurrent users. It's also for those customers who require advanced geospatial services.</p>|

## Next steps

Learn more about how to view and change pricing tiers:

> [!div class="nextstepaction"]	
> [Manage a pricing tier](how-to-manage-pricing-tier.md)
