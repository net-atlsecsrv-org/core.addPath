---
title: What is Azure Private Link?
description: Overview of Azure Private Link features, architecture, and implementation. Learn how Azure Private Endpoints and Azure Private Link service works and how to use them.
services: private-link
author: malopMSFT
# Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure Private Link so that I can securely connect to my Azure PaaS services within the virtual network.

ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
---
# What is Azure Private Link? 
Azure Private Link enables you to access Azure PaaS Services (for example, Azure Storage and SQL Database) and Azure hosted customer-owned/partner services over a [private endpoint](private-endpoint-overview.md) in your virtual network.

Traffic between your virtual network and the service travels the Microsoft backbone network. Exposing your service to the public internet is no longer necessary. You can create your own [private link service](private-link-service-overview.md) in your virtual network and deliver it to your customers. Setup and consumption using Azure Private Link is consistent across Azure PaaS, customer-owned, and shared partner services.

> [!IMPORTANT]
> Azure Private Link is now generally available. Both Private Endpoint and Private Link service (service behind standard load balancer) are generally available. Different Azure PaaS will onboard to Azure Private Link at different schedules. Check [availability](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) section below for accurate status of Azure PaaS on Private Link. For known limitations, see [Private Endpoint](private-endpoint-overview.md#limitations) and [Private Link Service](private-link-service-overview.md#limitations). 

## Key benefits
Azure Private Link provides the following benefits:  
- **Privately access services on the Azure platform**: Connect your virtual network to services in Azure without a public IP address at the source or destination. Service providers can render their services in their own virtual network and consumers can access those services in their local virtual network. The Private Link platform will handle the connectivity between the consumer and services over the Azure backbone network. 
 
- **On-premises and peered networks**: Access services running in Azure from on-premises over ExpressRoute private peering, VPN tunnels, and peered virtual networks using private endpoints. There's no need to set up ExpressRoute Microsoft peering or traverse the internet to reach the service. Private Link provides a secure way to migrate workloads to Azure.
 
- **Protection against data leakage**: A private endpoint is mapped to an instance of a PaaS resource instead of the entire service. Consumers can only connect to the specific resource. Access to any other resource in the service is blocked. This mechanism provides protection against data leakage risks. 
 
- **Global reach**: Connect privately to services running in other regions. The consumer's virtual network could be in region A and it can connect to services behind Private Link in region B.  
 
- **Extend to your own services**: Enable the same experience and functionality to render your service privately to consumers in Azure. By placing your service behind a standard Azure Load Balancer, you can enable it for Private Link. The consumer can then connect directly to your service using a private endpoint in their own virtual network. You can manage the connection requests using an approval call flow. Azure Private Link works for consumers and services belonging to different Azure Active Directory tenants. 

## Availability 
 The following table lists the Private Link services and the regions where they are available. 

|Supported services  |Available regions | Additional considerations | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link services behind standard Azure Load Balancer | All public regions<br/> All Government regions<br/>All China regions  | Supported on Standard Load Balancer | GA <br/> [Learn more](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Azure Blob storage (including Data Lake Storage Gen2)       |  All public regions<br/> All Government regions       |  Supported on Account Kind General Purpose V2 | GA <br/> [Learn more](/azure/storage/common/storage-private-endpoints)  |
| Azure Files | All public regions<br/> All Government regions      | |   GA <br/> [Learn more](/azure/storage/files/storage-files-networking-endpoints)   |
| Azure File Sync | All public regions      | |   GA <br/> [Learn more](/azure/storage/files/storage-sync-files-networking-endpoints)   |
| Azure Queue storage       |  All public regions<br/> All Government regions       |  Supported on Account Kind General Purpose V2 | GA <br/> [Learn more](/azure/storage/common/storage-private-endpoints)  |
| Azure Table storage       |  All public regions<br/> All Government regions       |  Supported on Account Kind General Purpose V2 | GA <br/> [Learn more](/azure/storage/common/storage-private-endpoints)  |
|  Azure SQL Database         | All public regions <br/> All Government regions<br/>All China regions      |  Supported for Proxy [connection policy](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) | GA <br/> [Learn more](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure Synapse Analytics (formerly SQL Data Warehouse)| All public regions <br/> All Government regions |  Supported for Proxy [connection policy](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) |GA <br/> [Learn more](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  All public regions<br/> All Government regions</br> All China regions | |GA <br/> [Learn more](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  Azure Database for PostgreSQL - Single server         | All public regions <br/> All Government regions<br/>All China regions     | Supported for General Purpose and Memory Optimized pricing tiers | GA <br/> [Learn more](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | All public regions<br/> All Government regions<br/>All China regions      |  | GA <br/> [Learn more](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | All public regions<br/> All Government regions<br/>All China regions     |  | GA <br/> [Learn more](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | All public regions<br/> All Government regions      |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes Service - Kubernetes API | All public regions      |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | All public regions <br/> All Government regions | Supported with service in Private Mode | GA   <br/> [Learn more](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Azure Container Registry | All public regions<br/> All Government regions    | Supported with premium tier of container registry. [Click for tiers](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| GA   <br/> [Learn more](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | All public regions      |  | Preview   |
|Azure Backup | All public regions<br/> All Government regions   |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | All public regions<br/>All Government regions      |   | GA   <br/> [Learn more](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | All public region<br/>All Government regions  | Supported with premium tier of Azure Service Bus. [Click for tiers](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | GA   <br/> [Learn more](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | All public regions      |  | Preview <br/> [Learn more](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Azure Event Grid| All public regions<br/> All Government regions       |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure Web Apps | All public regions      | Supported with PremiumV2 Windows and Linux and Elastic Premium Functions  | Preview   <br/> [Learn more](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Azure Machine Learning | All public regions    |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Azure Automation  | All public regions |  | Preview | |
| Azure IoT Hub | All public regions    |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | EAST US, SOUTH CENTRAL US,<br/>WEST US 2, All China regions      |  | Preview   <br/> [Learn more](https://aka.ms/asrs/privatelink)   |
| Azure Monitor <br/>(Log Analytics & Application Insights) | All public regions      |  | GA   <br/> [Learn more](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure Batch | All public regions except: GERMANY CENTRAL, GERMANY NORTHEAST <br/> All Government regions  | | GA <br/> [Learn more](https://docs.microsoft.com/azure/batch/private-connectivity) |
|Azure Data Factory | All public regions<br/> All Government regions<br/>All China regions    | Credentials need to be stored in an Azure key vault| GA   <br/> [Learn more](https://docs.microsoft.com/azure/data-factory/data-factory-private-link)   |



For the most up-to-date notifications, check the [Azure Private Link updates page](https://azure.microsoft.com/updates/?product=private-link).

## Logging and monitoring

Azure Private Link has integration with Azure Monitor. This combination allows:

 - Archival of logs to a storage account.
 - Streaming of events to your Event Hub.
 - Azure Monitor logging.

You can access the following information on Azure Monitor: 
- **Private endpoint**: 
    - Data processed by the Private Endpoint  (IN/OUT)
 
- **Private Link service**:
    - Data processed by the Private Link service (IN/OUT)
    - NAT port availability  
 
## Pricing   
For pricing details, see [Azure Private Link pricing](https://azure.microsoft.com/pricing/details/private-link/).
 
## FAQs  
For FAQs, see [Azure Private Link FAQs](private-link-faq.md).
 
## Limits  
For limits, see [Azure Private Link limits](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## Service Level Agreement
For SLA, see [SLA for Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## Next steps

- [Quickstart: Create a Private Endpoint using Azure portal](create-private-endpoint-portal.md)
- [Quickstart: Create a Private Link service by using the Azure portal](create-private-link-service-portal.md)


 
