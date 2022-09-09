---
title: Compare Azure Content Delivery Network (CDN) product features
description: Learn about the features that each Azure Content Delivery Network (CDN) product supports.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc

---

# What are the comparisons between Azure CDN product features?

Azure Content Delivery Network (CDN) includes four products: 

* **Azure CDN Standard from Microsoft**
* **Azure CDN Standard from Akamai**
* **Azure CDN Standard from Verizon**
* **Azure CDN Premium from Verizon**. 

The following table compares the features available with each product.

| **Performance features and optimizations** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamic site acceleration](./cdn-dynamic-site-acceleration.md)  | Offered via [Azure Front Door Service](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamic site acceleration - adaptive image compression](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamic site acceleration - object prefetch](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [General web delivery optimization](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Select this optimization type if your average file size is smaller than 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Video streaming optimization](./cdn-media-streaming-optimization.md)  | via General Web Delivery | **&#x2713;**  | via General Web Delivery |  via General Web Delivery |
| [Large file optimization](./cdn-large-file-optimization.md)  | via General Web Delivery | **&#x2713;**, Select this optimization type if your average file size is larger than 10 MB   | via General Web Delivery |  via General Web Delivery |
| Change optimization type | |**&#x2713;** | | |
| Origin Port |All TCP ports |[Allowed origin ports](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |All TCP ports |All TCP ports |
| [Global server load balancing (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Fast purge](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Purge all and Wildcard purge are not supported by Azure CDN from Akamai currently |**&#x2713;** |**&#x2713;** |
| [Asset pre-loading](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Cache/header settings (using [caching rules](cdn-caching-rules.md))  |**&#x2713;** using [Standard rules engine](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Customizable, rules based content delivery engine |**&#x2713;** using [Standard rules engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** using [rules engine](./cdn-verizon-premium-rules-engine.md) |
| Cache/header settings  |**&#x2713;** using [Standard rules engine](cdn-standard-rules-engine.md) | | |**&#x2713;** using [Premium rules engine](./cdn-verizon-premium-rules-engine.md) |
| URL redirect/rewrite |**&#x2713;** using [Standard rules engine](cdn-standard-rules-engine.md)  | | |**&#x2713;** using [Premium rules engine](./cdn-verizon-premium-rules-engine.md) |
| Mobile device rules  |**&#x2713;** using [Standard rules engine](cdn-standard-rules-engine.md) | | |**&#x2713;** using [Premium rules engine](./cdn-verizon-premium-rules-engine.md) |
| [Query string caching](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual-stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2 support](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Security** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS support with CDN endpoint | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Custom domain HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, Requires direct CNAME to enable |**&#x2713;** |**&#x2713;** |
| [Custom domain name support](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filtering](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Token authentication](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS protection](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Bring your own certificate](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Supported TLS Versions | TLS 1.2, TLS 1.0/1.1 - [Configurable](/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analytics and reporting** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnostic logs](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Core reports from Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Custom reports from Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Advanced HTTP reports](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Real-time stats](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Edge node performance](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Real-time alerts](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Ease of use** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Easy integration with Azure services, such as [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md), and [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Management via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md), or [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Compression MIME types](./cdn-improve-performance.md)  |Default only |Configurable |Configurable  |Configurable  |
| Compression encodings  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## Migration

For information about migrating an **Azure CDN Standard from Verizon** profile to **Azure CDN Premium from Verizon**, see [Migrate an Azure CDN profile from Standard Verizon to Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> There is an upgrade path from Standard Verizon to Premium Verizon, there is no conversion mechanism between other products at this time.

## Next steps

* Learn more about [Azure CDN](cdn-overview.md).
