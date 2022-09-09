---
title: Limitations - Azure Database for MariaDB
description: This article describes limitations in Azure Database for MariaDB, such as number of connection and storage engine options.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
---
# Limitations in Azure Database for MariaDB
The following sections describe capacity, storage engine support, privilege support, data manipulation statement support, and functional limits in the database service.

## Server parameters

> [!NOTE]
> If you are looking for min/max values for server parameters like `max_connections` and `innodb_buffer_pool_size`, this information has moved to the **[server parameters](./concepts-server-parameters.md)** article.

Azure Database for MariaDB supports tuning the values of server parameters. The min and max value of some parameters (ex. `max_connections`, `join_buffer_size`, `query_cache_size`) is determined by the pricing tier and vCores of the server. Refer to [server parameters](./concepts-server-parameters.md) for more information about these limits.

Upon initial deployment, an Azure for MariaDB server includes systems tables for time zone information, but these tables are not populated. The time zone tables can be populated by calling the `mysql.az_load_timezone` stored procedure from a tool like the MySQL command line or MySQL Workbench. Refer to the [Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) or [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) articles for how to call the stored procedure and set the global or session-level time zones.

Password plugins such as "validate_password" and "caching_sha2_password" are not supported by the service.

## Storage engine support

### Supported
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### Unsupported
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## Privileges & data manipulation support

Many server parameters and settings can inadvertently degrade server performance or negate ACID properties of the MariaDB server. To maintain the service integrity and SLA at a product level, this service does not expose multiple roles. 

The MariaDB service does not allow direct access to the underlying file system. Some data manipulation commands are not supported. 

## Privilege support

### Unsupported

The following are unsupported:
- DBA role: Restricted. Alternatively, you can use the administrator user (created during new server creation), allows you to perform most of DDL and DML statements. 
- SUPER privilege: Similarly, [SUPER privilege](https://mariadb.com/kb/en/library/grant/#global-privileges) is also restricted.
- DEFINER: Requires super privileges to create and is restricted. If importing data using a backup, remove the `CREATE DEFINER` commands manually or by using the `--skip-definer` command when performing a mysqldump.
- System databases: The [mysql system database](https://mariadb.com/kb/en/the-mysql-database-tables/) is read-only and used to support various PaaS functionality. You cannot make changes to the `mysql` system database.
- `SELECT ... INTO OUTFILE`: Not supported in the service.

### Supported
- `LOAD DATA INFILE` is supported, but the `[LOCAL]` parameter must be specified and directed to a UNC path (Azure storage mounted through SMB).

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
- [What's available in each service tier](concepts-pricing-tiers.md)
- [Supported MariaDB database versions](concepts-supported-versions.md)
