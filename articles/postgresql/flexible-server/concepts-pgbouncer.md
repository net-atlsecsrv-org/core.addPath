---
title: PgBouncer - Azure Database for PostgreSQL - Flexible Server
description: This article provides an overview with the built-in PgBouncer extension.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/20/2021
---

# PgBouncer in Azure Database for PostgreSQL - Flexible Server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is in preview

Azure Database for PostgreSQL – Flexible Server offers [PgBouncer](https://github.com/pgbouncer/pgbouncer) as a built-in connection pooling solution. This is an optional service that can be enabled on a per-database server basis and is supported with both public and private access. PgBouncer runs in the same virtual machine as the Postgres database server. Postgres uses a process-based model for connections which makes it expensive to maintain many idle connections. So, Postgres itself runs into resource constraints once the server runs more than a few thousand connections. The primary benefit of PgBouncer is to improve idle connections and short-lived connections at the database server.

PgBouncer uses a more lightweight model that utilizes asynchronous I/O, and only uses actual Postgres connections when needed, that is, when inside an open transaction, or when a query is active. This model can support thousands of connections more easily with low overhead and allows scaling to up to 10,000 connections with low overhead.

When enabled, PgBouncer runs on port 6432 on your database server. You can change your application’s database connection configuration to use the same host name, but change the port to 6432 to start using PgBouncer and benefit from improved idle connection scaling.

> [!Note]
> PgBouncer is supported only on General Purpose and Memory Optimized compute tiers.

## Enabling and configuring PgBouncer

In order to enable PgBouncer, you can navigate to the “Server Parameters” blade in the Azure portal, and search for “PgBouncer” and change the pgbouncer.enabled setting to “true” for PgBouncer to be enabled. There is no need to restart the server. However, to set other PgBouncer parameters, see the limitations section.

You can configure PgBouncer, settings with these parameters:

| Parameter Name             | Description | Default | 
|----------------------|--------|-------------|
| pgbouncer.default_pool_size | Set this parameter value to the number of connections per user/database pair      | 50       | 
| pgBouncer.max_client_conn | Set this parameter value to the highest number of client connections to PgBouncer that you want to support      | 5000     | 
| pgBouncer.pool_mode | Set this parameter value to TRANSACTION for transaction pooling (which is the recommended setting for most workloads).      | TRANSACTION     |
| pgBouncer.min_pool_size | Add more server connections to pool if below this number.    |   0 (Disabled)   |
| pgBouncer.stats_users | Optional. Set this parameter value to the name of an existing user, to be able to log in to the special PgBouncer statistics database (named “PgBouncer”)    |      |

> [!Note] 
> Upgrading of PgBouncer will be managed by Azure.

## Switching your application to use PgBouncer

In order to start using PgBouncer, follow these steps:
1. Connect to your database server, but use port **6432** instead of the regular port 5432 -- verify that this connection works
```azurecli-interactive
psql "host=myPgServer.postgres.database.azure.com port=6432 dbname=postgres user=myUser password=myPassword sslmode=require"
```
2. Test your application in a QA environment against PgBouncer, to make sure you don’t have any compatibility problems. The PgBouncer project provides a compatibility matrix, and we recommend using **transaction pooling** for most users: https://www.PgBouncer.org/features.html#sql-feature-map-for-pooling-modes.
3. Change your production application to connect to port **6432** instead of **5432**, and monitor for any application side errors that may point to any compatibility issues.

> [!Note] 
> Even if you had enabled PgBouncer, you can still connect to the database server directly over port 5432 using the same host name.

## PgBouncer in Zone-redundant high availability

In zone-redundant high availability configured servers, the primary server runs the PgBouncer. You can connect to the primary server's PgBouncer over port 6432. After a failover, the PgBouncer is restarted on the newly promoted standby, which is the new primary server. So your application connection string remains the same post failover. 

## Using PgBouncer with other connection pools

In some cases, you may already have an application side connection pool, or have PgBouncer set up on your application side such as an AKS side car. In these cases,  it can still be useful to utilize the built-in PgBouncer, as it provides idle connection scaling benefits.

Utilizing an application side pool together with PgBouncer on the database server can be beneficial. Here, the application side pool brings the benefit of reduced initial connection latency (as the initial roundtrip to initialize the connection is much faster), and the database-side PgBouncer provides idle connection scaling.

## Limitations
 
* PgBouncer is currently not supported with Burstable server compute tier. 
* If you change the compute tier from General Purpose or Memory Optimized to Burstable tier, you will lose the PgBouncer capability.
* Whenever the server is restarted during scale operations, HA failover, or a restart, the PgBouncer is also restarted along with the server virtual machine. Hence the existing connections have to be re-established.
* Due to a known issue, the portal does not show all PgBouncer parameters. Once you enable PgBouncer and save the parameter, you have to exit Parameter screen (for example, click Overview) and then get back to Parameters page. 
  
## Next steps

- Learn about [networking concepts](./concepts-networking.md)
- Flexible server [overview](./overview.md)
