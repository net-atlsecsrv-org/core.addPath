---
title: MySQL server operational best practices - Azure Database for MySQL
description: This article describes the best practices to operate your MySQL database on Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
---

# Best practices for server operations on Azure Database for MySQL -Single server

Learn about the best practices for working with Azure Database for MySQL. As we add new capabilities to the platform, we will continue to focus on refining the best practices detailed in this section.

## Azure Database for MySQL Operational Guidelines 

The following are operational guidelines that should be followed when working with your Azure Database for MySQL to improve the performance of your database: 

* **Co-location**: To reduce network latency, place the client and the database server are in the same Azure region.

* **Monitor your memory, CPU, and storage usage**: You can [setup alerts](howto-alert-on-metric.md) to notify you when usage patterns change or when you approach the capacity of your deployment, so that you can maintain system performance and availability. 

* **Scale up your DB instance**: You can [scale up](howto-create-manage-server-portal.md) when you are approaching storage capacity limits. You should have some buffer in storage and memory to accommodate unforeseen increases in demand from your applications. You can also [enable the storage autogrow](howto-auto-grow-storage-portal.md) feature 'ON' just to ensure that the service automatically scales the storage as it nears the storage limits. 

* **Configure backups**: Enable [local or geo-redundant backups](howto-restore-server-portal.md#set-backup-configuration) based on the requirement of the business. Also, you modify the retention period on how long the backups are available for business continuity. 

* **Increase I/O capacity**: If your database workload requires more I/O than you have provisioned, recovery or other transactional operations for your database will be slow. To increase the I/O capacity of a server instance, do any or all of the following: 

    * Azure database for MySQL provides IOPS scaling at the rate of three IOPS per GB storage provisioned. [Increase the provisioned storage](howto-create-manage-server-portal.md#scale-storage-up) to scale the IOPS for better performance. 

    * If you are already using Provisioned IOPS storage, provision [additional throughput capacity](howto-create-manage-server-portal.md#scale-storage-up). 

* **Scale compute**: Database workload can also be limited due to CPU or memory and this can have serious impact on the transaction processing. Note that compute (pricing tier) can be scaled up or down between [General Purpose or Memory Optimized](concepts-pricing-tiers.md) tiers only. 

* **Test for failover**: Manually test failover for your server instance to understand how long the process takes for your use case and to ensure that the application that accesses your server instance can automatically connect to the new server instance after failover.

* **Use primary key**: Make sure your tables have a primary or unique key as you operate on the Azure Database for MySQL. This helps in a lot taking backups, replica etc. and improves performance.

* **Configure TTL value**: If your client application is caching the Domain Name Service (DNS) data of your server instances, set a time-to-live (TTL) value of less than 30 seconds. Because the underlying IP address of a server instance can change after a failover, caching the DNS data for an extended time can lead to connection failures if your application tries to connect to an IP address that no longer is in service.

* Use connection pooling to avoid hitting the [maximum connection limits](concepts-server-parameters.md#max_connections)and use retry logic to avoid intermittent connection issues. 

* If you are using replica, use [ProxySQL to balance off load](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) between the primary server and the readable secondary replica server. See the setup steps here. </br> 

* When provisioning the resource, make sure you [enabled the autogrow](howto-auto-grow-storage-portal.md) for your Azure Database for MySQL. This does not add any additional cost and will protect the database from any storage bottlenecks that you might run into. </br> 


### Using InnoDB with Azure Database for MySQL

*	If using `ibdata1` feature, which is a system tablespace data file cannot shrink or be purged by dropping the data from the table, or moving the table to file-per-table `tablespaces`.

* For a database greater than 1 TB in size, you should create the table in **innodb_file_per_table** `tablespace`. For a single table that is larger than 1 TB in size, you should the [partition](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) table.

*	For a server that has a large number of `tablespace`, the engine startup will be very slow due to the sequential tablespace scan during MySQL startup or failover. 

* Set innodb_file_per_table = ON before you create a table, if the total table number is less than 500.

* If you have more than 500 tables in a database, then review the table size for each individual table. For a large table, you should still consider using the file-per-table tablespace to avoid the system tablespace file hit max storage limit.

> [!NOTE]
> For tables with size less than 5GB, consider using the system tablespace 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Partition](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) your table at table creation if you have a very large table might potentially grow beyond 1 TB.

* Use multiple MySQL servers and spread the tables across those servers. Avoid putting too many tables on a single server if you have around 10000 tables or more. 

## Next steps
- [Best practice for performance of Azure Database for MySQL](concept-performance-best-practices.md)
- [Best practice for monitoring your Azure Database for MySQL](concept-monitoring-best-practices.md)
