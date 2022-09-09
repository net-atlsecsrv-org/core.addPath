---
title: Limitations - Azure Database for MariaDB
description: This article describes limitations in Azure Database for MariaDB, such as number of connection and storage engine options.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
---
# Limitations in Azure Database for MariaDB
The following sections describe capacity, storage engine support, privilege support, data manipulation statement support, and functional limits in the database service.

## Maximum connections
The maximum number of connections per pricing tier and vCores are as follows:

|**Pricing Tier**|**vCore(s)**| **Max Connections**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|General Purpose| 2| 600|
|General Purpose| 4| 1250|
|General Purpose| 8| 2500|
|General Purpose| 16| 5000|
|General Purpose| 32| 10000|
|General Purpose| 64| 20000|
|Memory Optimized| 2| 800|
|Memory Optimized| 4| 2500|
|Memory Optimized| 8| 5000|
|Memory Optimized| 16| 10000|
|Memory Optimized| 32| 20000|

When connections exceed the limit, you may receive the following error:
> ERROR 1040 (08004): Too many connections

## Storage engine support

### Supported
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### Unsupported
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## Privilege support

### Unsupported
- DBA role: 
Many server parameters and settings can inadvertently degrade server performance or negate ACID properties of the DBMS. As such, to maintain the service integrity and SLA at a product level, this service does not expose the DBA role. The default user account, which is constructed when a new database instance is created, allows that user to perform most of DDL and DML statements in the managed database instance.
- SUPER privilege: 
Similarly [SUPER privilege](https://mariadb.com/kb/en/library/grant/#global-privileges) is also restricted.
- DEFINER: 
Requires super privileges to create and is restricted. If importing data using a backup, remove the `CREATE DEFINER` commands manually or by using the `--skip-definer` command when performing a mysqldump.

## Data manipulation statement support

### Supported
- `LOAD DATA INFILE` is supported, but the `[LOCAL]` parameter must be specified and directed to a UNC path (Azure storage mounted through SMB).

### Unsupported
- `SELECT ... INTO OUTFILE`

## Functional limitations

### Scale operations
- Dynamic scaling to and from the Basic pricing tiers is currently not supported.
- Decreasing server storage size is not supported.

### Server version upgrades
- Automated migration between major database engine versions is currently not supported.

### Point-in-time-restore
- When using the PITR feature, the new server is created with the same configurations as the server it is based on.
- Restoring a deleted server is not supported.

### Subscription management
- Dynamically moving pre-created servers across subscription and resource group is currently not supported.

### VNet service endpoints
- Support for VNet service endpoints is only for General Purpose and Memory Optimized servers.

### Storage size
- Please refer to [pricing tiers](concepts-pricing-tiers.md) for the storage size limits per pricing tier.

## Current known issues
- MariaDB server instance displays the incorrect server version after connection is established. To get the correct server instance engine version, use the `select version();` command.

## Next steps
- [What’s available in each service tier](concepts-pricing-tiers.md)
- [Supported MariaDB database versions](concepts-supported-versions.md)
