---
title: Overview - Azure Database for MySQL - Flexible Server
description: Learn about the Azure Database for MySQL Flexible server, a relational database service in the Microsoft cloud based on the MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
---
# Azure Database for MySQL - Flexible Server (Preview)

Azure Database for MySQL powered by the MySQL community edition is available in two deployment modes:
- Single Server 
- Flexible Server (Preview)

In this article, we will provide an overview and introduction to core concepts of flexible server deployment model. 

## Overview

Azure Database for MySQL Flexible Server is a fully managed database service designed to provide more granular control and flexibility over database management functions and configuration settings. In general, the service provides more flexibility and server configuration customizations based on the user requirements. The flexible server architecture allows users to opt for high availability within single availability zone and across multiple availability zones. Flexible servers also provide better cost optimization controls with ability to stop/start your server and burstable skus, ideal for workloads that do not need full compute capacity continuously. The service currently supports community version of MySQL 5.7. The service is currently in preview, available today in wide variety of [Azure regions](https://azure.microsoft.com/global-infrastructure/services/).

Flexible servers are best suited for 
- Application developments requiring better control and customizations.
- Zone redundant high availability
- Managed maintenance windows

![Flexible Server conceptual diagram](media/overview/1-flexible-server-conceptual-diagram.png) 

## High availability within and across availability zones

The flexible server deployment model is designed to support high availability within single availability zone and across multiple availability zones. The architecture separates compute and storage. The database engine runs on a virtual machine, while data files reside on Azure storage. The storage maintains three locally redundant synchronous copies of the database files ensuring data durability at all times. 

Within a single availability zone, if the server goes down due to planned or unplanned events, the service maintains high availability of the servers using following automated procedure:

1. A new compute VM is provisioned.
2. The storage with data files is mapped to the new Virtual Machine
3. MySQL database engine is brought online on the new Virtual Machine.
4. Client applications can reconnect once the server is ready to accept connections.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Single Zone high availability conceptual diagram"::: 

If zone redundant high availability is configured, the service provisions and maintains a hot standby server across availability zone within the same Azure region. The data changes on the source server is synchronously replicated to the standby server to ensure zero data loss. With zone redundant high availability, once the planned or unplanned failover event is triggered, the standby server comes online immediately and is available to process incoming transactions. The typical failover time ranges from 60-120 seconds. This allows the service to support high availability and provide improved resiliency with tolerance for single availability zone failures in a given Azure region. 

See [high availability concepts](concepts-high-availability.md) for more details.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Zone Redundant high availability conceptual diagram"::: 

## Automated patching with managed maintenance window

The service performs automated patching of the underlying hardware, OS, and database engine. The patching includes security and software updates. For MySQL engine, minor version upgrades are also included as part of the planned maintenance release. Users can configure the patching schedule to be system managed or define their custom schedule. During the maintenance schedule, the patch is applied and server may require a restart as part of the patching process to complete the update. With the custom schedule, users can make their patching cycle predictable and choose a maintenance window with minimum impact to the business. In general, the service follows monthly release schedule as part of the continuous integration and release. 

## Automatic backups

The flexible server service automatically creates server backups and stores them in user configured locally redundant or geo-redundant storage. Backups can be used to restore your server to any point-in-time within the backup retention period. The default backup retention period is seven days. The retention can be optionally configured up to 35 days. All backups are encrypted using AES 256-bit encryption. 

See [Backup concepts](concepts-backup-restore.md) to learn more.

## Network Isolation

You have two networking options to connect to your Azure Database for MySQL Flexible Server. The options are **private access (VNet integration)** and **public access (allowed IP addresses)**. 

* **Private access (VNet Integration)** – You can deploy your flexible server into your [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Azure virtual networks provide private and secure network communication. Resources in a virtual network can communicate through private IP addresses.

   Choose the VNet Integration option if you want the following capabilities:
   * Connect from Azure resources in the same virtual network to your flexible server using private IP addresses
   * Use VPN or ExpressRoute to connect from non-Azure resources to your flexible server
   * No public endpoint

* **Public access (allowed IP addresses)** – You can deploy your flexible server with a public endpoint. The public endpoint is a publicly resolvable DNS address. The phrase “allowed IP addresses” refers to a range of IPs you choose to give permission to access your server. These permissions are called **firewall rules**.

See [Networking concepts](concepts-networking.md) to learn more.

## Adjust performance and scale within seconds

The flexible server service is available in three SKU tiers: Burstable, General Purpose, and Memory Optimized. The Burstable tier is best suited for low-cost development and low concurrency workloads that do not need full compute capacity continuously. The General Purpose and Memory Optimized are better suited for production workloads requiring high concurrency, scale, and predictable performance. You can build your first app on a small database for a few dollars a month, and then seamlessly adjust the scale to meet the needs of your solution. The storage scaling is online and supports storage autogrowth. Dynamic scalability enables your database to transparently respond to rapidly changing resource requirements. You only pay for the resources you consume. 

See [Compute and Storage concepts](concepts-compute-storage.md) to learn more.

## Stop/Start server to optimize cost

The flexible server service allows you to stop and start server on-demand to optimize cost. The compute tier billing is stopped immediately when the server is stopped. This can allow you to have significant cost savings during development, testing and for time-bound predictable production workloads. The server remains in stopped state for seven days unless re-started sooner. 

## Enterprise grade security and privacy

The flexible server service uses the FIPS 140-2 validated cryptographic module for storage encryption of data at-rest. Data, including backups, and temporary files created while running queries are encrypted. The service uses the AES 256-bit cipher included in Azure storage encryption, and the keys can be system managed (default). 

The service encrypts data in-motion with transport layer security enforced by default. Flexible Servers only supports encrypted connections using Transport Layer Security (TLS 1.2) and all incoming connections with TLS 1.0 and TLS 1.1 will be denied. 

See [how to use encrypted connections to flexible servers](/articles/mysql/flexible-server/how-to-connect-tls-ssl.md) to learn more.

Flexible servers allows full private access to the servers using [Azure virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) (VNet) integration. Servers in Azure virtual network can only be reached and connected through private IP addresses. With VNet integration, public access is denied and servers cannot be reached using public endpoints. 

See [Networking concepts](concepts-networking.md) to learn more.


## Monitoring and alerting

The flexible server service is equipped with built-in performance monitoring and alerting features. All Azure metrics have a one-minute frequency, and each metric provides 30 days of history. You can configure alerts on the metrics. The service exposes host server metrics to monitor resources utilization and allows configuring slow query logs. Using these tools, you can quickly optimize your workloads, and configure your server for best performance. 

See [Monitoring concepts](concepts-monitoring.md) to learn more.

## Migration

The service runs the community version of MySQL. This allows full application compatibility and requires minimal refactoring cost to migrate existing application developed on MySQL engine to single server service. The migration to the single server can be performed using one of the following options:

- **Dump and Restore** – For offline migrations, where users can afford some downtime, dump and restore using community tools like mysqldump/mydumper can provide fastest way to migrate. See Migrate using dump and restore for details. 
- **Azure Database Migration Service** – For seamless and simplified migrations to single server with minimal downtime, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online) can be leveraged. 

## Next steps
Now that you've read an introduction to Azure Database for MySQL single server deployment mode, you're ready to:

- Create your first server. 
  - [Create an Azure Database for MySQL flexible server using Azure portal](quickstart-create-server-portal.md)
  - [Create an Azure Database for MySQL flexible server using Azure CLI](quickstart-create-server-cli.md)
  - [Manage an Azure Database for MySQL Flexible Server using the Azure CLI](how-to-manage-server-portal.md)

- Build your first app using your preferred language:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
