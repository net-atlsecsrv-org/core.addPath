---
title: Limitations - Azure Database for MySQL
description: This article describes limitations in Azure Database for MySQL, such as number of connection and storage engine options.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
---
# Limitations in Azure Database for MySQL
The following sections describe capacity, storage engine support, privilege support, data manipulation statement support, and functional limits in the database service. Also see [general limitations](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) applicable to the MySQL database engine.

## Server parameters

The minimum and maximum values of several popular server parameters are determined by the pricing tier and vCores. Refer to the below tables for limits.

### max_connections

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|General Purpose|2|300|10|600|
|General Purpose|4|625|10|1250|
|General Purpose|8|1250|10|2500|
|General Purpose|16|2500|10|5000|
|General Purpose|32|5000|10|10000|
|General Purpose|64|10000|10|20000|
|Memory Optimized|2|600|10|800|
|Memory Optimized|4|1250|10|2500|
|Memory Optimized|8|2500|10|5000|
|Memory Optimized|16|5000|10|10000|
|Memory Optimized|32|10000|10|20000|

When connections exceed the limit, you may receive the following error:
> ERROR 1040 (08004): Too many connections

> [!IMPORTANT]
> For best experience, we recommend that you use a connection pooler like ProxySQL to efficiently manage connections.

Creating new client connections to MySQL takes time and once established, these connections occupy database resources, even when idle. Most applications request many short-lived connections, which compounds this situation. The result is fewer resources available for your actual workload leading to decreased performance. A connection pooler that decreases idle connections and reuses existing connections will help avoid this. To learn about setting up ProxySQL, visit our [blog post](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### query_cache_size

The query cache is turned off by default. To enable the query cache, configure the `query_cache_type` parameter. 

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) to learn more about this parameter.

> [!NOTE]
> The query cache is deprecated as of MySQL 5.7.20 and has been removed in MySQL 8.0

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|Not configurable in Basic tier|N/A|N/A|
|Basic|2|Not configurable in Basic tier|N/A|N/A|
|General Purpose|2|0|0|16777216|
|General Purpose|4|0|0|33554432|
|General Purpose|8|0|0|67108864|
|General Purpose|16|0|0|134217728|
|General Purpose|32|0|0|134217728|
|General Purpose|64|0|0|134217728|
|Memory Optimized|2|0|0|33554432|
|Memory Optimized|4|0|0|67108864|
|Memory Optimized|8|0|0|134217728|
|Memory Optimized|16|0|0|134217728|
|Memory Optimized|32|0|0|134217728|

### sort_buffer_size

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) to learn more about this parameter.

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|Not configurable in Basic tier|N/A|N/A|
|Basic|2|Not configurable in Basic tier|N/A|N/A|
|General Purpose|2|524288|32768|4194304|
|General Purpose|4|524288|32768|8388608|
|General Purpose|8|524288|32768|16777216|
|General Purpose|16|524288|32768|33554432|
|General Purpose|32|524288|32768|33554432|
|General Purpose|64|524288|32768|33554432|
|Memory Optimized|2|524288|32768|8388608|
|Memory Optimized|4|524288|32768|16777216|
|Memory Optimized|8|524288|32768|33554432|
|Memory Optimized|16|524288|32768|33554432|
|Memory Optimized|32|524288|32768|33554432|

### join_buffer_size

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) to learn more about this parameter.

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|Not configurable in Basic tier|N/A|N/A|
|Basic|2|Not configurable in Basic tier|N/A|N/A|
|General Purpose|2|262144|128|268435455|
|General Purpose|4|262144|128|536870912|
|General Purpose|8|262144|128|1073741824|
|General Purpose|16|262144|128|2147483648|
|General Purpose|32|262144|128|4294967295|
|General Purpose|64|262144|128|4294967295|
|Memory Optimized|2|262144|128|536870912|
|Memory Optimized|4|262144|128|1073741824|
|Memory Optimized|8|262144|128|2147483648|
|Memory Optimized|16|262144|128|4294967295|
|Memory Optimized|32|262144|128|4294967295|

### max_heap_table_size

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) to learn more about this parameter.

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|Not configurable in Basic tier|N/A|N/A|
|Basic|2|Not configurable in Basic tier|N/A|N/A|
|General Purpose|2|16777216|16384|268435455|
|General Purpose|4|16777216|16384|536870912|
|General Purpose|8|16777216|16384|1073741824|
|General Purpose|16|16777216|16384|2147483648|
|General Purpose|32|16777216|16384|4294967295|
|General Purpose|64|16777216|16384|4294967295|
|Memory Optimized|2|16777216|16384|536870912|
|Memory Optimized|4|16777216|16384|1073741824|
|Memory Optimized|8|16777216|16384|2147483648|
|Memory Optimized|16|16777216|16384|4294967295|
|Memory Optimized|32|16777216|16384|4294967295|

### tmp_table_size

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) to learn more about this parameter.

|**Pricing Tier**|**vCore(s)**|**Default value**|**Min value**|**Max value**|
|---|---|---|---|---|
|Basic|1|Not configurable in Basic tier|N/A|N/A|
|Basic|2|Not configurable in Basic tier|N/A|N/A|
|General Purpose|2|16777216|1024|67108864|
|General Purpose|4|16777216|1024|134217728|
|General Purpose|8|16777216|1024|268435456|
|General Purpose|16|16777216|1024|536870912|
|General Purpose|32|16777216|1024|1073741824|
|General Purpose|64|16777216|1024|1073741824|
|Memory Optimized|2|16777216|1024|134217728|
|Memory Optimized|4|16777216|1024|268435456|
|Memory Optimized|8|16777216|1024|536870912|
|Memory Optimized|16|16777216|1024|1073741824|
|Memory Optimized|32|16777216|1024|1073741824|

## Storage engine support

### Supported
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### Unsupported
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## Privilege support

### Unsupported
- DBA role: 
Many server parameters and settings can inadvertently degrade server performance or negate ACID properties of the DBMS. As such, to maintain the service integrity and SLA at a product level, this service does not expose the DBA role. The default user account, which is constructed when a new database instance is created, allows that user to perform most of DDL and DML statements in the managed database instance. 
- SUPER privilege: 
Similarly [SUPER privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) is also restricted.
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
- Automated migration between major database engine versions is currently not supported. If you would like to upgrade to the next major version, take a [dump and restore](./concepts-migrate-dump-restore.md) it to a server that was created with the new engine version.

### Point-in-time-restore
- When using the PITR feature, the new server is created with the same configurations as the server it is based on.
- Restoring a deleted server is not supported.

### VNet service endpoints
- Support for VNet service endpoints is only for General Purpose and Memory Optimized servers.

### Storage size
- Please refer to [pricing tiers](concepts-pricing-tiers.md) for the storage size limits per pricing tier.

## Current known issues
- MySQL server instance displays the wrong server version after connection is established. To get the correct server instance engine version, use the `select version();` command.

## Next steps
- [What's available in each service tier](concepts-pricing-tiers.md)
- [Supported MySQL database versions](concepts-supported-versions.md)
