---
title: Configure Data-in Replication to replicate data into Azure Database for MariaDB.
description: This article describes how to set up Data-in Replication for Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
---

# How to configure Azure Database for MariaDB Data-in Replication

In this article, you will learn how to set up Data-in Replication in the Azure Database for MariaDB service by configuring the master and replica servers. Data-in Replication allows you to synchronize data from a master MariaDB server running on-premises, in virtual machines, or database services hosted by other cloud providers into a replica in the Azure Database for MariaDB service. We recommanded you setup the data-in replication with [Global Transaction ID](https://mariadb.com/kb/en/library/gtid/) when your master server's version is 10.2 or above.

This article assumes that you have at least some prior experience with MariaDB servers and databases.

## Create a MariaDB server to be used as replica

1. Create a new Azure Database for MariaDB server

   Create a new MariaDB server (ex. "replica.mariadb.database.azure.com"). Refer to [Create an Azure Database for MariaDB server by using the Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md) for server creation. This server is the "replica" server in Data-in Replication.

   > [!IMPORTANT]
   > The Azure Database for MariaDB server must be created in the General Purpose or Memory Optimized pricing tiers.
   > 

2. Create same user accounts and corresponding privileges

   User accounts are not replicated from the master server to the replica server. If you plan on providing users with access to the replica server, you need to manually create all accounts and corresponding privileges on this newly created Azure Database for MariaDB server.

## Configure the master server
The following steps prepare and configure the MariaDB server hosted on-premises, in a virtual machine, or database service hosted by other cloud providers for Data-in Replication. This server is the "master" in Data-in replication. 

1. Turn on binary logging

   Check to see if binary logging has been enabled on the master by running the following command: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   If the variable [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) is returned with the value “ON", binary logging is enabled on your server. 

   If `log_bin` is returned with the value “OFF”, turn on binary logging by editing your my.cnf file so that `log_bin=ON` and restart your server for the change to take effect.

2. Master server settings

   Data-in Replication requires parameter `lower_case_table_names` to be consistent between the master and replica servers. This parameter is 1 by default in Azure Database for MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Create a new replication role and set up permission

   Create a user account on the master server that is configured with replication privileges. This can be done through SQL commands or a tool like MySQL Workbench. Consider whether you plan on replicating with SSL as this will need to be specified when creating the user. Refer to the MariaDB documentation to understand how to [add user accounts](https://mariadb.com/kb/en/library/create-user/) on your master server. 

   In the commands below, the new replication role created is able to access the master from any machine, not just the machine that hosts the master itself. This is done by specifying "syncuser\@'%'" in the create user command. See the MariaDB documentation to learn more about [specifying account names](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL Command**

   *Replication with SSL*

   To require SSL for all user connections, use the following command to create a user: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replication without SSL*

   If SSL is not required for all connections, use the following command to create a user:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   To create the replication role in MySQL Workbench, open the **Users and Privileges** panel from the **Management** panel. Then click on **Add Account**. 
 
   ![Users and Privileges](./media/howto-data-in-replication/users_privileges.png)

   Type in the username into the **Login Name** field. 

   ![Sync user](./media/howto-data-in-replication/syncuser.png)
 
   Click on the **Administrative Roles** panel and then select **Replication Slave** from the list of **Global Privileges**. Then click on **Apply** to create the replication role.

   ![Replication Slave](./media/howto-data-in-replication/replicationslave.png)


4. Set the master server to read-only mode

   Before starting to dump out the database, the server needs to be placed in read-only mode. While in read-only mode, the master will be unable to process any write transactions. Evaluate the impact to your business and schedule the read-only window in an off-peak time if necessary.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Get binary log file name and offset

   Run the [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) command to determine the current binary log file name and offset.
	
   ```sql
   show master status;
   ```
   The results should be like following. Make sure to note the binary file name as it will be used in later steps.

   ![Master Status Results](./media/howto-data-in-replication/masterstatus.png)
   
6. Get GTID position (Optional, needed for replication with GTID)

   Run the function [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) command to get the GTID position for the correspond binlog file name and offset.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## Dump and restore master server

1. Dump all databases from master server

   You can use mysqldump to dump databases from your master. For details, refer to [Dump & Restore](howto-migrate-dump-restore.md). It is unnecessary to dump MySQL library and test library.

2. Set master server to read/write mode

   Once the database has been dumped, change the master MariaDB server back to read/write mode.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restore dump file to new server

   Restore the dump file to the server created in the Azure Database for MariaDB service. Refer to [Dump & Restore](howto-migrate-dump-restore.md) for how to restore a dump file to a MariaDB server. If the dump file is large, upload it to a virtual machine in Azure within the same region as your replica server. Restore it to the Azure Database for MariaDB server from the virtual machine.

## Link master and replica servers to start Data-in Replication

1. Set master server

   All Data-in Replication functions are done by stored procedures. You can find all procedures at [Data-in Replication Stored Procedures](reference-data-in-stored-procedures.md). The stored procedures can be run in the MySQL shell or MySQL Workbench.

   To link two servers and start replication, login to the target replica server in the Azure DB for MariaDB service and set the external instance as the master server. This is done by using the `mysql.az_replication_change_master` or `mysql.az_replication_change_master_with_gtid` stored procedure on the Azure DB for MariaDB server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   or
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: hostname of the master server
   - master_user: username for the master server
   - master_password: password for the master server
   - master_log_file: binary log file name from running `show master status`
   - master_log_pos: binary log position from running `show master status`
   - master_gtid_pos: GTID position from running `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA certificate’s context. If not using SSL, pass in empty string.
       - It is recommended to pass this parameter in as a variable. See the following examples for more information.

   **Examples**

   *Replication with SSL*

   The variable `@cert` is created by running the following commands: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replication with SSL is set up between a master server hosted in the domain “companya.com” and a replica server hosted in Azure Database for MariaDB. This stored procedure is run on the replica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Replication without SSL*

   Replication without SSL is set up between a master server hosted in the domain “companya.com” and a replica server hosted in Azure Database for MariaDB. This stored procedure is run on the replica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Start replication

   Call the `mysql.az_replication_start` stored procedure to initiate replication.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Check replication status

   Call the [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) command on the replica server to view the replication status.
	
   ```sql
   show slave status;
   ```

   If the state of `Slave_IO_Running` and `Slave_SQL_Running` are "yes" and the value of `Seconds_Behind_Master` is “0”, replication is working well. `Seconds_Behind_Master` indicates how late the replica is. If the value is not "0", it means that the replica is processing updates. 

4. Update correspond server variables to make data-in replication more safe (Only needed for replication without GTID)
	
	Because of MariaDB native replication limitation, you need to setup [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) and [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variables on replication without GTID scenario. We recommand you check your slave server's `sync_master_info` and `sync_relay_log_info` variables and change them ot `1` if you want to make sure the data-in replication is stable.
	
## Other stored procedures

### Stop replication

To stop replication between the master and replica server, use the following stored procedure:

```sql
CALL mysql.az_replication_stop;
```

### Remove replication relationship

To remove the relationship between master and replica server, use the following stored procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### Skip replication error

To skip a replication error and allow replication to proceed, use the following stored procedure:
	
```sql
CALL mysql.az_replication_skip_counter;
```

## Next steps
- Learn more about [Data-in Replication](concepts-data-in-replication.md) for Azure Database for MariaDB.
