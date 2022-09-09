---
title: Compute and Storage Options - Azure Database for PostgreSQL - Flexible Server
description: This article describes the compute and storage options in Azure Database for PostgreSQL - Flexible Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
---

# Compute and Storage options in Azure Database for PostgreSQL - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview

You can create an Azure Database for PostgreSQL server in one of three different pricing tiers: Burstable, General Purpose, and Memory Optimized. The pricing tiers are differentiated by the amount of compute in vCores that can be provisioned, memory per vCore, and the storage technology used to store the data. All resources are provisioned at the PostgreSQL server level. A server can have one or many databases.

| Resource / Tier | **Burstable** | **General Purpose** | **Memory Optimized** |
|:---|:----------|:--------------------|:---------------------|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memory per vCore | Variable | 4 GB | 6.75 to 8 GB |
| Storage size | 32 GB to 16 TB | 32 GB to 16 TB | 32 GB to 16 TB |
| Database backup retention period | 7 to 35 days | 7 to 35 days | 7 to 35 days |

To choose a pricing tier, use the following table as a starting point.

| Pricing tier | Target workloads |
|:-------------|:-----------------|
| Burstable | Best for workloads that don’t need the full CPU continuously. |
| General Purpose | Most business workloads that require balanced compute and memory with scalable I/O throughput. Examples include servers for hosting web and mobile apps and other enterprise applications.|
| Memory Optimized | High-performance database workloads that require in-memory performance for faster transaction processing and higher concurrency. Examples include servers for processing real-time data and high-performance transactional or analytical apps.|

After you create a server, the compute tier, number of vCores and storage size can be changed up or down within seconds. You also can independently adjust the backup retention period up or down. For more information, see the [Scale resources](#scale-resources) section.

## Compute tiers, vCores, and server types

Compute resources can be selected based on the tier, vCores and memory size. vCores represent the logical CPU of the underlying hardware.

The detailed specifications of the available server types are as follows:

| SKU Name             | vCores | Memory Size | Max Supported IOPS | Max Supported I/O bandwidth |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Burstable**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/sec                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/sec                  |
| **General Purpose**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/sec                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/sec                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/sec                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/sec                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/sec                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/sec                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/sec                 |
| **Memory Optimized** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/sec                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/sec                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/sec                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/sec                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/sec                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/sec                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/sec                 |

## Storage

The storage you provision is the amount of storage capacity available to your Azure Database for PostgreSQL server. The storage is used for the database files, temporary files, transaction logs, and the PostgreSQL server logs. The total amount of storage you provision also defines the I/O capacity available to your server.

Storage is available in the following fixed sizes:

| Disk size | IOPS |
|:---|:---|
| 32 GiB | Provisioned 120, Up to 3,500 |
| 64 GiB | Provisioned 240, Up to 3,500 |
| 128 GiB | Provisioned 500, Up to 3,500 |
| 256 GiB | Provisioned 1100, Up to 3,500 |
| 512 GiB | Provisioned 2300, Up to 3,500 |
| 1 TiB | 5,000 |
| 2 TiB | 7,500 |
| 4 TiB | 7,500 |
| 8 TiB | 16,000 |
| 16 TiB | 18,000 |

Note that IOPS are also constrained by your VM type. Even though you can select any storage size independent of the server type, you may not be able to use all IOPS that the storage provides, especially when you choose a server with a small number of vCores.

You can add additional storage capacity during and after the creation of the server.

>[!NOTE]
> Storage can only be scaled up, not down.

You can monitor your I/O consumption in the Azure portal or by using Azure CLI commands. The relevant metrics to monitor are [storage limit, storage percentage, storage used, and IO percent](concepts-monitoring.md).

### Maximum IOPS for your configuration

|SKU Name            |Storage Size in GiB                       |32 |64 |128 |256 |512  |1,024|2,048|4,096|8,192 |16,384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maximum IOPS                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Burstable**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640*|640* |640* |640* |640* |640*  |640*  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280*|1280*|1280*|1280*|1280* |1280* |
|**General Purpose** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|D4s_v3              |6,400 IOPS                                |120|240|500 |1100|2300 |5000 |6400*|6400*|6400* |6400* |
|D8s_v3              |12,800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800*|12800*|
|D16s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Memory Optimized**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|E4s_v3              |6,400 IOPS                                |120|240|500 |1100|2300 |5000 |6400*|6400*|6400* |6400* |
|E8s_v3              |12,800 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800*|12800*|
|E16s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |18,000 IOPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

When marked with a \*, IOPS are limited by the VM type you selected. Otherwise IOPS are limited by the selected storage size.

### Maximum I/O bandwidth (MiB/sec) for your configuration

|SKU Name            |Storage Size, GiB                             |32 |64 |128 |256 |512  |1,024|2,048|4,096|8,192 |16,384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Storage Bandwidth, MiB/sec**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Burstable**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/sec                                    |10*|10*|10* |10* |10*  |10*  |10*  |10*  |10*   |10*   |
|B2s                 |15 MiB/sec                                    |15*|15*|15* |15* |15*  |15*  |15*  |15*  |15*   |15*   |
|**General Purpose** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/sec                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|D4s_v3              |96 MiB/sec                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|D8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|D16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|D32s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Memory Optimized**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/sec                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|E4s_v3              |96 MiB/sec                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|E8s_v3              |192 MiB/sec                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|E16s_v3             |384 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|E32s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/sec                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

When marked with a \*, I/O bandwidth is limited by the VM type you selected. Otherwise I/O bandwidth is limited by the selected storage size.

### Reaching the storage limit

When you reach the storage limit, the server will start returning errors and prevent any further modifications. This may also cause problems with other operational activities, such as backups and WAL archival.

We recommend to actively monitor the disk space that is in use, and increase the disk size ahead of any out of storage situation. You can set up an alert to notify you when your server storage is approaching out of disk so you can avoid any issues with running out of disk. For more information, see the documentation on [how to set up an alert](howto-alert-on-metrics.md).

### Storage auto-grow

Storage auto-grow is not yet available for Flexible Server.

## Backup

The service automatically takes backups of your server. You can select a retention period from a range of 7 to 35 days. Learn more about backups in the [concepts article](concepts-backup-restore.md).

## Scale resources

After you create your server, you can independently change the vCores, the compute tier, the amount of storage, and the backup retention period. The number of vCores can be scaled up or down. The backup retention period can be scaled up or down from 7 to 35 days. The storage size can only be increased. Scaling of the resources can be done either through the portal or Azure CLI.

> [!NOTE]
> The storage size can only be increased. You cannot go back to a smaller storage size after the increase.

When you change the number of vCores or the compute tier, the server is restarted for the new server type to take effect. During the moment when the system switches over to the new server, no new connections can be established, and all uncommitted transactions are rolled back. This window varies, but in most cases, is less than a minute. Scaling the storage works the same way, and also requires a short restart.

Changing the backup retention period is an online operation.

## Pricing

For the most up-to-date pricing information, see the service [pricing page](https://azure.microsoft.com/pricing/details/PostgreSQL/). To see the cost for the configuration you want, the [Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) shows the monthly cost on the **Pricing tier** tab based on the options you select. If you don't have an Azure subscription, you can use the Azure pricing calculator to get an estimated price. On the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/) website, select **Add items**, expand the **Databases** category, and choose **Azure Database for PostgreSQL** to customize the options.

## Next steps

- Learn how to [create a PostgreSQL server in the portal](how-to-manage-server-portal.md).
- Learn about [service limits](concepts-limits.md).
