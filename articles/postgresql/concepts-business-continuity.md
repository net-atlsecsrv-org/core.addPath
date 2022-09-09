---
title: Business continuity - Azure Database for PostgreSQL - Single Server
description: This article describes business continuity (point in time restore, data center outage, geo-restore, replicas) when using Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
---

# Overview of business continuity with Azure Database for PostgreSQL - Single Server

This overview describes the capabilities that Azure Database for PostgreSQL provides for business continuity and disaster recovery. Learn about options for recovering from disruptive events that could cause data loss or cause your database and application to become unavailable. Learn what to do when a user or application error affects data integrity, an Azure region has an outage, or your application requires maintenance.

## Features that you can use to provide business continuity

As you develop your business continuity plan, you need to understand the maximum acceptable time before the application fully recovers after the disruptive event - this is your Recovery Time Objective (RTO). You also need to understand the maximum amount of recent data updates (time interval) the application can tolerate losing when recovering after the disruptive event - this is your Recovery Point Objective (RPO).

Azure Database for PostgreSQL provides business continuity features that include geo-redundant backups with the ability to initiate geo-restore, and deploying read replicas in a different region. Each has different characteristics for the recovery time and the potential data loss. With [Geo-restore](concepts-backup.md) feature, a new server is created using the backup data that is replicated from another region. The overall time it takes to restore and recover depends on the size of the database and the amount of logs to recover. The overall time to establish the server varies from few minutes to few hours. With [read replicas](concepts-read-replicas.md), transaction logs from the primary are asynchronously streamed to the replica. The lag between the primary and the replica depends on the latency between the sites and also the amount of data to be transmitted. In the event of a primary site failure such as availability zone fault, promoting the replica provides a shorter RTO and reduced data loss. 

The following table compares RTO and RPO in a typical scenario:

| **Capability** | **Basic** | **General Purpose** | **Memory optimized** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Point in Time Restore from backup | Any restore point within the retention period | Any restore point within the retention period | Any restore point within the retention period |
| Geo-restore from geo-replicated backups | Not supported | RTO - Varies <br/>RPO < 1 h | RTO - Varies <br/>RPO < 1 h |
| Read replicas | RTO - Minutes <br/>RPO < 5 min | RTO - Minutes <br/>RPO < 5 min| RTO - Minutes <br/>RPO < 5 min|

> [!IMPORTANT]
> The expected RTO and RPO mentioned here are for reference purposes only. No SLAs are offered for these metrics.

## Recover a server after a user or application error

You can use the service’s backups to recover a server from various disruptive events. A user may accidentally delete some data, inadvertently drop an important table, or even drop an entire database. An application might accidentally overwrite good data with bad data due to an application defect, and so on.

You can perform a **point-in-time-restore** to create a copy of your server to a known good point in time. This point in time must be within the backup retention period you have configured for your server. After the data is restored to the new server, you can either replace the original server with the newly restored server or copy the needed data from the restored server into the original server.

> [!IMPORTANT]
> Deleted servers **cannot** be restored. If you delete the server, all databases that belong to the server are also deleted and cannot be recovered. Use [Azure resource lock](../azure-resource-manager/management/lock-resources.md) to help prevent accidental deletion of your server.

## Recover from an Azure data center outage

Although rare, an Azure data center can have an outage. When an outage occurs, it causes a business disruption that might only last a few minutes, but could last for hours.

One option is to wait for your server to come back online when the data center outage is over. This works for applications that can afford to have the server offline for some period of time, for example a development environment. When a data center has an outage, you do not know how long the outage might last, so this option only works if you don't need your server for a while.

## Geo-restore

The geo-restore feature restores the server using geo-redundant backups. The backups are hosted in your server's [paired region](../best-practices-availability-paired-regions.md). You can restore from these backups to any other region. The geo-restore creates a new server with the data from the backups. Learn more about geo-restore from the [backup and restore concepts article](concepts-backup.md).

> [!IMPORTANT]
> Geo-restore is only possible if you provisioned the server with geo-redundant backup storage. If you wish to switch from locally redundant to geo-redundant backups for an existing server, you must take a dump using pg_dump of your existing server and restore it to a newly created server configured with geo-redundant backups.

## Cross-region read replicas
You can use cross region read replicas to enhance your business continuity and disaster recovery planning. Read replicas are updated asynchronously using PostgreSQL's physical replication technology. Learn more about read replicas, available regions, and how to fail over from the [read replicas concepts article](concepts-read-replicas.md). 

## FAQ
### Where does Azure Database for PostgreSQL store customer data?
By default, Azure Database for PostgreSQL doesn't move or store customer data out of the region it is deployed in. However, customers can optionally chose to enable [geo-redundant backups](concepts-backup.md#backup-redundancy-options) or create [cross-region read replica](concepts-read-replicas.md#cross-region-replication) for storing data in another region.


## Next steps
- Learn more about the [automated backups in Azure Database for PostgreSQL](concepts-backup.md). 
- Learn how to restore using [the Azure portal](howto-restore-server-portal.md) or [the Azure CLI](howto-restore-server-cli.md).
- Learn about [read replicas in Azure Database for PostgreSQL](concepts-read-replicas.md).
