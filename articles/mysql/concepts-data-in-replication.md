---
title: Data-in replication - Azure Database for MySQL
description: Learn about using data-in replication to synchronize from an external server into the Azure Database for MySQL service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
---

# Replicate data into Azure Database for MySQL

Data-in Replication allows you to synchronize data from an external MySQL server into the Azure Database for MySQL service. The external server can be on-premises, in virtual machines, or a database service hosted by other cloud providers. Data-in Replication is based on the binary log (binlog) file position-based replication native to MySQL. To learn more about binlog replication, see the [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## When to use Data-in Replication
The main scenarios to consider using Data-in Replication are:

- **Hybrid Data Synchronization:** With Data-in Replication, you can keep data synchronized between your on-premises servers and Azure Database for MySQL. This synchronization is useful for creating hybrid applications. This method is appealing when you have an existing local database server but want to move the data to a region closer to end users.
- **Multi-Cloud Synchronization:** For complex cloud solutions, use Data-in Replication to synchronize data between Azure Database for MySQL and different cloud providers, including virtual machines and database services hosted in those clouds.
 
For migration scenarios, use the [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## Limitations and considerations

### Data not replicated
The [*mysql system database*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) on the source server isn't replicated. Changes to accounts and permissions on the source server aren't replicated. If you create an account on the source server and this account needs to access the replica server, manually create the same account on the replica server side. To understand what tables are contained in the system database, see the [MySQL manual](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### Filtering
To skip replicating tables from your source server (hosted on-premises, in virtual machines, or a database service hosted by other cloud providers), the `replicate_wild_ignore_table` parameter is supported. Optionally, update this parameter on the replica server hosted in Azure using the [Azure portal](howto-server-parameters.md) or [Azure CLI](howto-configure-server-parameters-using-cli.md).

Review the [MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) to learn more about this parameter.

### Requirements
- The source server version must be at least MySQL version 5.6. 
- The source and replica server versions must be the same. For example, both must be MySQL version 5.6 or both must be MySQL version 5.7.
- Each table must have a primary key.
- Source server should use the MySQL InnoDB engine.
- User must have permissions to configure binary logging and create new users on the source server.
- If the source server has SSL enabled, ensure the SSL CA certificate provided for the domain has been included in the `mysql.az_replication_change_master` stored procedure. Refer to the following [examples](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) and the `master_ssl_ca` parameter.
- Ensure the source server's IP address has been added to the Azure Database for MySQL replica server's firewall rules. Update firewall rules using the [Azure portal](./howto-manage-firewall-using-portal.md) or [Azure CLI](./howto-manage-firewall-using-cli.md).
- Ensure the machine hosting the source server allows both inbound and outbound traffic on port 3306.
- Ensure the the source server has a **public IP address**, the DNS is publicly accessible, or has a fully qualified domain name (FQDN).

### Other
- Data-in replication is only supported in General Purpose and Memory Optimized pricing tiers.
- Global transaction identifiers (GTID) are not supported.

## Next steps
- Learn how to [set up data-in replication](howto-data-in-replication.md)
- Learn about [replicating in Azure with read replicas](concepts-read-replicas.md)
- Learn about how to [migrate data with minimal downtime using DMS](howto-migrate-online.md)