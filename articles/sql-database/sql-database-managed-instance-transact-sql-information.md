---
title: Azure SQL Database managed instance T-SQL differences | Microsoft Docs
description: This article discusses the T-SQL differences between a managed instance in Azure SQL Database and SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: 
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova 
ms.date: 08/12/2019
ms.custom: seoapril2019
---

# Managed instance T-SQL differences, limitations, and known issues

This article summarizes and explains the differences in syntax and behavior between Azure SQL Database managed instance and on-premises SQL Server Database Engine. The managed instance deployment option provides high compatibility with on-premises SQL Server Database Engine. Most of the SQL Server database engine features are supported in a managed instance.

![Migration](./media/sql-database-managed-instance/migration.png)

There are some PaaS limitations that are introduced in Managed Instance and some behavior changes compared to SQL Server. The differences are divided into the following categories: <a name="Differences"></a>

- [Availability](#availability) includes the differences in [Always-On](#always-on-availability) and [backups](#backup).
- [Security](#security) includes the differences in [auditing](#auditing), [certificates](#certificates), [credentials](#credential), [cryptographic providers](#cryptographic-providers), [logins and users](#logins-and-users), and the [service key and service master key](#service-key-and-service-master-key).
- [Configuration](#configuration) includes the differences in [buffer pool extension](#buffer-pool-extension), [collation](#collation), [compatibility levels](#compatibility-levels), [database mirroring](#database-mirroring), [database options](#database-options), [SQL Server Agent](#sql-server-agent), and [table options](#tables).
- [Functionalities](#functionalities) include [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [distributed transactions](#distributed-transactions), [extended events](#extended-events), [external libraries](#external-libraries), [filestream and FileTable](#filestream-and-filetable), [full-text Semantic Search](#full-text-semantic-search), [linked servers](#linked-servers), [PolyBase](#polybase), [Replication](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [stored procedures, functions, and triggers](#stored-procedures-functions-and-triggers).
- [Environment settings](#Environment) such as VNets and subnet configurations.

Most of these features are architectural constraints and represent service features.

This page also explains [Temporary known issues](#Issues) that are discovered in managed instance, which will be resolved in the future.

## Availability

### <a name="always-on-availability"></a>Always On

[High availability](sql-database-high-availability.md) is built into managed instance and can't be controlled by users. The following statements aren't supported:

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- The [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) clause of the [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) statement

### Backup

Managed instances have automatic backups, so users can create full database `COPY_ONLY` backups. Differential, log, and file snapshot backups aren't supported.

- With a managed instance, you can back up an instance database only to an Azure Blob storage account:
  - Only `BACKUP TO URL` is supported.
  - `FILE`, `TAPE`, and backup devices aren't supported.
- Most of the general `WITH` options are supported.
  - `COPY_ONLY` is mandatory.
  - `FILE_SNAPSHOT` isn't supported.
  - Tape options: `REWIND`, `NOREWIND`, `UNLOAD`, and `NOUNLOAD` aren't supported.
  - Log-specific options: `NORECOVERY`, `STANDBY`, and `NO_TRUNCATE` aren't supported.

Limitations: 

- With a managed instance, you can back up an instance database to a backup with up to 32 stripes, which is enough for databases up to 4 TB if backup compression is used.
- You can't execute `BACKUP DATABASE ... WITH COPY_ONLY` on a database that's encrypted with service-managed Transparent Data Encryption (TDE). Service-managed TDE forces backups to be encrypted with an internal TDE key. The key can't be exported, so you can't restore the backup. Use automatic backups and point-in-time restore, or use [customer-managed (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) instead. You also can disable encryption on the database.
- The maximum backup stripe size by using the `BACKUP` command in a managed instance is 195 GB, which is the maximum blob size. Increase the number of stripes in the backup command to reduce individual stripe size and stay within this limit.

    > [!TIP]
    > To work around this limitation, when you back up a database from either SQL Server in an on-premises environment or in a virtual machine, you can:
    >
    > - Back up to `DISK` instead of backing up to `URL`.
    > - Upload the backup files to Blob storage.
    > - Restore into the managed instance.
    >
    > The `Restore` command in a managed instance supports bigger blob sizes in the backup files because a different blob type is used for storage of the uploaded backup files.

For information about backups using T-SQL, see [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## Security

### Auditing

The key differences between auditing in databases in Azure SQL Database and databases in SQL Server are:

- With the managed instance deployment option in Azure SQL Database, auditing works at the server level. The `.xel` log files are stored in Azure Blob storage.
- With the single database and elastic pool deployment options in Azure SQL Database, auditing works at the database level.
- In SQL Server on-premises or virtual machines, auditing works at the server level. Events are stored on file system or Windows event logs.
 
XEvent auditing in managed instance supports Azure Blob storage targets. File and Windows logs aren't supported.

The key differences in the `CREATE AUDIT` syntax for auditing to Azure Blob storage are:

- A new syntax `TO URL` is provided that you can use to specify the URL of the Azure Blob storage container where the `.xel` files are placed.
- The syntax `TO FILE` isn't supported because a managed instance can't access Windows file shares.

For more information, see: 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### Certificates

A managed instance can't access file shares and Windows folders, so the following constraints apply:

- The `CREATE FROM`/`BACKUP TO` file isn't supported for certificates.
- The `CREATE`/`BACKUP` certificate from `FILE`/`ASSEMBLY` isn't supported. Private key files can't be used. 

See [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) and [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Workaround**: Script for the certificate or private key, store as .sql file, and create from binary:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### Credential

Only Azure Key Vault and `SHARED ACCESS SIGNATURE` identities are supported. Windows users aren't supported.

See [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) and [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### Cryptographic providers

A managed instance can't access files, so cryptographic providers can't be created:

- `CREATE CRYPTOGRAPHIC PROVIDER` isn't supported. See [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` isn't supported. See [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### Logins and users

- SQL logins created by using `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, and `FROM SID` are supported. See [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (Azure AD) server principals (logins) created with the [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) syntax or the [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) syntax are supported (public preview). These logins are created at the server level.

    Managed instance supports Azure AD database principals with the syntax `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. This feature is also known as Azure AD contained database users.

- Windows logins created with the `CREATE LOGIN ... FROM WINDOWS` syntax aren't supported. Use Azure Active Directory logins and users.
- The Azure AD user who created the instance has [unrestricted admin privileges](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Non-administrator Azure AD database-level users can be created by using the `CREATE USER ... FROM EXTERNAL PROVIDER` syntax. See [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Azure AD server principals (logins) support SQL features within one managed instance only. Features that require cross-instance interaction, no matter whether they're within the same Azure AD tenant or different tenants, aren't supported for Azure AD users. Examples of such features are:

  - SQL transactional replication.
  - Link server.

- Setting an Azure AD login mapped to an Azure AD group as the database owner isn't supported.
- Impersonation of Azure AD server-level principals by using other Azure AD principals is supported, such as the [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) clause. EXECUTE AS limitations are:

  - EXECUTE AS USER isn't supported for Azure AD users when the name differs from the login name. An example is when the user is created through the syntax CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] and impersonation is attempted through EXEC AS USER = _myAadUser_. When you create a **USER** from an Azure AD server principal (login), specify the user_name as the same login_name from **LOGIN**.
  - Only the SQL Server-level principals (logins) that are part of the `sysadmin` role can execute the following operations that target Azure AD principals:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Public preview limitations for Azure AD server principals (logins):

  - Active Directory admin limitations for managed instance:

    - The Azure AD admin used to set up the managed instance can't be used to create an Azure AD server principal (login) within the managed instance. You must create the first Azure AD server principal (login) by using a SQL Server account that's a `sysadmin` role. This temporary limitation will be removed after Azure AD server principals (logins) become generally available. If you try to use an Azure AD admin account to create the login, you see the following error: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Currently, the first Azure AD login created in the master database must be created by the standard SQL Server account (non-Azure AD) that's a `sysadmin` role by using [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. After general availability, this limitation will be removed. Then you can create an initial Azure AD login by using the Active Directory admin for managed instance.
    - DacFx (export/import) used with SQL Server Management Studio or SqlPackage isn't supported for Azure AD logins. This limitation will be removed after Azure AD server principals (logins) become generally available.
    - Using Azure AD server principals (logins) with SQL Server Management Studio:

      - Scripting Azure AD logins that use any authenticated login isn't supported.
      - IntelliSense doesn't recognize the CREATE LOGIN FROM EXTERNAL PROVIDER statement and shows a red underline.

- Only the server-level principal login, which is created by the managed instance provisioning process, members of the server roles, such as `securityadmin` or `sysadmin`, or other logins with ALTER ANY LOGIN permission at the server level can create Azure AD server principals (logins) in the master database for managed instance.
- If the login is a SQL principal, only logins that are part of the `sysadmin` role can use the create command to create logins for an Azure AD account.
- The Azure AD login must be a member of an Azure AD within the same directory that's used for Azure SQL Database managed instance.
- Azure AD server principals (logins) are visible in Object Explorer starting with SQL Server Management Studio 18.0 preview 5.
- Overlapping Azure AD server principals (logins) with an Azure AD admin account is allowed. Azure AD server principals (logins) take precedence over the Azure AD admin when you resolve the principal and apply permissions to the managed instance.
- During authentication, the following sequence is applied to resolve the authenticating principal:

    1. If the Azure AD account exists as directly mapped to the Azure AD server principal (login), which is present in sys.server_principals as type "E," grant access and apply permissions of the Azure AD server principal (login).
    2. If the Azure AD account is a member of an Azure AD group that's mapped to the Azure AD server principal (login), which is present in sys.server_principals as type "X," grant access and apply permissions of the Azure AD group login.
    3. If the Azure AD account is a special portal-configured Azure AD admin for managed instance, which doesn't exist in managed instance system views, apply special fixed permissions of the Azure AD admin for managed instance (legacy mode).
    4. If the Azure AD account exists as directly mapped to an Azure AD user in a database, which is present in sys.database_principals as type "E," grant access and apply permissions of the Azure AD database user.
    5. If the Azure AD account is a member of an Azure AD group that's mapped to an Azure AD user in a database, which is present in sys.database_principals as type "X," grant access and apply permissions of the Azure AD group login.
    6. If there's an Azure AD login mapped to either an Azure AD user account or an Azure AD group account, which resolves to the user who's authenticating, all permissions from this Azure AD login are applied.

### Service key and service master key

- [Master key backup](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) isn't supported (managed by SQL Database service).
- [Master key restore](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) isn't supported (managed by SQL Database service).
- [Service master key backup](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) isn't supported (managed by SQL Database service).
- [Service master key restore](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) isn't supported (managed by SQL Database service).

## Configuration

### Buffer pool extension

- [Buffer pool extension](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) isn't supported.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` isn't supported. See [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### Collation

The default instance collation is `SQL_Latin1_General_CP1_CI_AS` and can be specified as a creation parameter. See [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations).

### Compatibility levels

- Supported compatibility levels are 100, 110, 120, 130, 140 and 150.
- Compatibility levels below 100 aren't supported.
- The default compatibility level for new databases is 140. For restored databases, the compatibility level remains unchanged if it was 100 and above.

See [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### Database mirroring

Database mirroring isn't supported.

- `ALTER DATABASE SET PARTNER` and `SET WITNESS` options aren't supported.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` isn't supported.

For more information, see [ALTER DATABASE SET PARTNER and SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) and [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### Database options

- Multiple log files aren't supported.
- In-memory objects aren't supported in the General Purpose service tier. 
- There's a limit of 280 files per General Purpose instance, which implies a maximum of 280 files per database. Both data and log files in the General Purpose tier are counted toward this limit. [The Business Critical tier supports 32,767 files per database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- The database can't contain filegroups that contain filestream data. Restore fails if .bak contains `FILESTREAM` data. 
- Every file is placed in Azure Blob storage. IO and throughput per file depend on the size of each individual file.

#### CREATE DATABASE statement

The following limitations apply to `CREATE DATABASE`:

- Files and filegroups can't be defined. 
- The `CONTAINMENT` option isn't supported. 
- `WITH` options aren't supported. 
   > [!TIP]
   > As a workaround, use `ALTER DATABASE` after `CREATE DATABASE` to set database options to add files or to set containment. 

- The `FOR ATTACH` option isn't supported.
- The `AS SNAPSHOT OF` option isn't supported.

For more information, see [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### ALTER DATABASE statement

Some file properties can't be set or changed:

- A file path can't be specified in the `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL statement. Remove `FILENAME` from the script because a managed instance automatically places the files. 
- A file name can't be changed by using the `ALTER DATABASE` statement.

The following options are set by default and can't be changed:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

The following options can't be modified:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

For more information, see [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### SQL Server Agent

- Enabling and disabling SQL Server Agent is currently not supported in managed instance. SQL Agent is always running.
- SQL Server Agent settings are read only. The procedure `sp_set_agent_properties` isn't supported in managed instance. 
- Jobs
  - T-SQL job steps are supported.
  - The following replication jobs are supported:
    - Transaction-log reader
    - Snapshot
    - Distributor
  - SSIS job steps are supported.
  - Other types of job steps aren't currently supported:
    - The merge replication job step isn't supported. 
    - Queue Reader isn't supported. 
    - Command shell isn't yet supported.
  - Managed instances can't access external resources, for example, network shares via robocopy. 
  - SQL Server Analysis Services aren't supported.
- Notifications are partially supported.
- Email notification is supported, although it requires that you configure a Database Mail profile. SQL Server Agent can use only one Database Mail profile, and it must be called `AzureManagedInstance_dbmail_profile`. 
  - Pager isn't supported.
  - NetSend isn't supported.
  - Alerts aren't yet supported.
  - Proxies aren't supported.
- EventLog isn't supported.

The following SQL Agent features currently aren't supported:

- Proxies
- Scheduling jobs on an idle CPU
- Enabling or disabling an Agent
- Alerts

For information about SQL Server Agent, see [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### Tables

The following table types aren't supported:

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (not supported only in General Purpose tier)

For information about how to create and alter tables, see [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) and [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## Functionalities

### Bulk insert / OPENROWSET

A managed instance can't access file shares and Windows folders, so the files must be imported from Azure Blob storage:

- `DATASOURCE` is required in the `BULK INSERT` command while you import files from Azure Blob storage. See [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` is required in the `OPENROWSET` function when you read the content of a file from Azure Blob storage. See [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` can be used to read data from other Azure SQL single databases, managed instances or SQL Server instances. Other sources such as Oracle databases or Excel files are not supported.

### CLR

A managed instance can't access file shares and Windows folders, so the following constraints apply:

- Only `CREATE ASSEMBLY FROM BINARY` is supported. See [CREATE ASSEM
BLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` isn't supported. See [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` can't reference files. See [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### Database Mail (db_mail)
 - `sp_send_dbmail` cannot send attachments using @file_attachments parameter. Local file system and external shares or Azure Blob Storage are not accessible from this procedure.
 - See the known issues related to `@query` parameter and authentication.
 
### DBCC

Undocumented DBCC statements that are enabled in SQL Server aren't supported in managed instances.

- Only a limited number of Global Trace flags are supported. Session-level `Trace flags` aren't supported. See [Trace flags](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) and [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) work with the limited number of global trace-flags.
- [DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) with options REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST, and REPAIR_REBUILD cannot be used because database cannot be set in `SINGLE_USER` mode - see [ALTER DATABASE differences](#alter-database-statement). Potential database corruptions are handled by Azure support team. Contact Azure support if you are noticing database corruption that should be fixed.

### Distributed transactions

MSDTC and [elastic transactions](sql-database-elastic-transactions-overview.md) currently aren't supported in managed instances.

### Extended Events

Some Windows-specific targets for Extended Events (XEvents) aren't supported:

- The `etw_classic_sync` target isn't supported. Store `.xel` files in Azure Blob storage. See [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- The `event_file` target isn't supported. Store `.xel` files in Azure Blob storage. See [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### External libraries

In-database R and Python, external libraries aren't yet supported. See [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### Filestream and FileTable

- Filestream data isn't supported.
- The database can't contain filegroups with `FILESTREAM` data.
- `FILETABLE` isn't supported.
- Tables can't have `FILESTREAM` types.
- The following functions aren't supported:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

For more information, see [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) and [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### Full-text Semantic Search

[Semantic Search](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) isn't supported.

### Linked servers

Linked servers in managed instances support a limited number of targets:

- Supported targets are Managed Instances, Single Databases, and SQL Server instances. 
- Linked servers don't support distributed writable transactions (MS DTC).
- Targets that aren't supported are files, Analysis Services, and other RDBMS. Try to use native CSV import from Azure Blob Storage using `BULK INSERT` or `OPENROWSET` as an alternative for file import.

Operations

- Cross-instance write transactions aren't supported.
- `sp_dropserver` is supported for dropping a linked server. See [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- The `OPENROWSET` function can be used to execute queries only on SQL Server instances. They can be either managed, on-premises, or in virtual machines. See [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- The `OPENDATASOURCE` function can be used to execute queries only on SQL Server instances. They can be either managed, on-premises, or in virtual machines. Only the `SQLNCLI`, `SQLNCLI11`, and `SQLOLEDB` values are supported as a provider. An example is `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. See [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
- Linked servers cannot be used to read files (Excel, CSV) from the network shares. Try to use [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) or [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) that reads CSV files from Azure Blob Storage. Track this requests on [managed instance Feedback item](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### PolyBase

External tables that reference the files in HDFS or Azure Blob storage aren't supported. For information about PolyBase, see [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### Replication

- Snapshot and Bi-directional replication types are supported. Merge replication, Peer-to-peer replication, and updatable subscriptions are not supported.
- [Transactional Replication](sql-database-managed-instance-transactional-replication.md) is available for public preview on managed instance with some constraints:
    - All types of replication participants (Publisher, Distributor, Pull Subscriber, and Push Subscriber) can be placed on managed instances, but the publisher and the distributor must be either both in the cloud or both on-premises.
    - Managed instances can communicate with the recent versions of SQL Server. See the supported versions [here](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
    - Transactional Replication has some [additional networking requirements](sql-database-managed-instance-transactional-replication.md#requirements).

For information about configuring replication, see the [replication tutorial](replication-with-sql-database-managed-instance.md).


If replication is enabled on a database in a [failover group](sql-database-auto-failover-group.md), the managed instance administrator must clean up all publications on the old primary and reconfigure them on the new primary after a failover occurs. The following activities are needed in this scenario:

1. Stop all replication jobs running on the database, if there are any.
2. Drop subscription metadata from publisher by running the following script on publisher database:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Drop subscription metadata from the subscriber. Run the following script in the subscription database on subscriber instance:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Forcefully drop all replication objects from publisher by running the following script in the published database:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Forcefully drop old distributor from original primary instance (if failing back over to an old primary that used to have a distributor). Run the following script on the master database in old distributor managed instance:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

### RESTORE statement 

- Supported syntax:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Unsupported syntax:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Source: 
  - `FROM URL` (Azure Blob storage) is the only supported option.
  - `FROM DISK`/`TAPE`/backup device isn't supported.
  - Backup sets aren't supported.
- `WITH` options aren't supported, such as no `DIFFERENTIAL` or `STATS`.
- `ASYNC RESTORE`: Restore continues even if the client connection breaks. If your connection is dropped, you can check the `sys.dm_operation_status` view for the status of a restore operation, and for a CREATE and DROP database. See [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

The following database options are set or overridden and can't be changed later: 

- `NEW_BROKER` if the broker isn't enabled in the .bak file. 
- `ENABLE_BROKER` if the broker isn't enabled in the .bak file. 
- `AUTO_CLOSE=OFF` if a database in the .bak file has `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` if a database in the .bak file has `SIMPLE` or `BULK_LOGGED` recovery mode.
- A memory-optimized filegroup is added and called XTP if it wasn't in the source .bak file. 
- Any existing memory-optimized filegroup is renamed to XTP. 
- `SINGLE_USER` and `RESTRICTED_USER` options are converted to `MULTI_USER`.

Limitations: 

- Backups of the corrupted databases might be restored depending on the type of the corruption, but automated backups will not be taken until the corruption is fixed. Make sure that you run `DBCC CHECKDB` on the source instance and use backup `WITH CHECKSUM` in order to prevent this issue.
- Restore of `.BAK` file of a database that contains any limitation described in this document (for example, `FILESTREAM` or `FILETABLE` objects) cannot be restored on Managed Instance.
- `.BAK` files that contain multiple backup sets can't be restored. 
- `.BAK` files that contain multiple log files can't be restored.
- Backups that contain databases bigger than 8 TB, active in-memory OLTP objects, or number of files that would exceed 280 files per instance can't be restored on a General Purpose instance. 
- Backups that contain databases bigger than 4 TB or in-memory OLTP objects with the total size larger than the size described in [resource limits](sql-database-managed-instance-resource-limits.md) cannot be restored on Business Critical instance.
For information about restore statements, see [RESTORE statements](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > The same limitations apply to built-in point-in-time restore operation. As an example, General Purpose database greater than 4 TB cannot be restored on Business Critical instance. Business Critical database with In-memory OLTP files or more than 280 files cannot be restored on General Purpose instance.

### Service broker

Cross-instance service broker isn't supported:

- `sys.routes`: As a prerequisite, you must select the address from sys.routes. The address must be LOCAL on every route. See [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: You can't use `CREATE ROUTE` with `ADDRESS` other than `LOCAL`. See [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: You can't use `ALTER ROUTE` with `ADDRESS` other than `LOCAL`. See [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### Stored procedures, functions, and triggers

- `NATIVE_COMPILATION` isn't supported in the General Purpose tier.
- The following [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) options aren't supported: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` isn't supported. See [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` isn't supported. See [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` aren't supported, which includes `sp_addextendedproc` and `sp_dropextendedproc`. See [Extended stored procedures](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db`, and `sp_detach_db` aren't supported. See [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql), and [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### System functions and variables

The following variables, functions, and views return different results:

- `SERVERPROPERTY('EngineEdition')` returns the value 8. This property uniquely identifies a managed instance. See [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` returns NULL because the concept of instance as it exists for SQL Server doesn't apply to a managed instance. See [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` returns a full DNS "connectable" name, for example, my-managed-instance.wcus17662feb9ce98.database.windows.net. See [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` returns a full DNS "connectable" name, such as `myinstance.domain.database.windows.net` for the properties "name" and "data_source." See [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` returns NULL because the concept of service as it exists for SQL Server doesn't apply to a managed instance. See [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` is supported. It returns NULL if the Azure AD login isn't in sys.syslogins. See [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` isn't supported. The wrong data is returned, which is a temporary known issue. See [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Environment"></a>Environment constraints

### Subnet
-  You cannot place any other resources (for example virtual machines) in the subnet where you have deployed your managed instance. Deploy these resources using a different subnet.
- Subnet must have sufficient number of available [IP addresses](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Minimum is 16, while recommendation is to have at least 32 IP addresses in the subnet.
- [Service endpoints cannot be associated with the managed instance's subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Make sure that the service endpoints option is disabled when you create the virtual network.
- The number of vCores and types of instances that you can deploy in a region have some [constraints and limits](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- There are some [security rules that must be applied on the subnet](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### VNET
- VNet can be deployed using Resource Model - Classic Model for VNet is not supported.
- After a managed instance is created, moving the managed instance or VNet to another resource group or subscription is not supported.
- Some services such as App Service Environments, Logic apps, and managed instances (used for Geo-replication, Transactional replication, or via linked servers) cannot access managed instances in different regions if their VNets are connected using [global peering](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). You can connect to these resources via ExpressRoute or VNet-to-VNet through VNet Gateways.

### TEMPDB

The maximum file size of `tempdb` can't be greater than 24 GB per core on a General Purpose tier. The maximum `tempdb` size on a Business Critical tier is limited by the instance storage size. `Tempdb` log file size is limited to 120 GB both on General Purpose and Business Critical tiers. Some queries might return an error if they need more than 24 GB per core in `tempdb` or if they produce more than 120 GB of log data.

### Error logs

A managed instance places verbose information in error logs. There are many internal system events that are logged in the error log. Use a custom procedure to read error logs that filters out some irrelevant entries. For more information, see [managed instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

## <a name="Issues"></a> Known issues

### Wrong error returned while trying to remove a file that is not empty

**Date:** Oct 2019

SQL Server/Managed Instance [don't allow user to drop a file that is not empty](https://docs.microsoft.com/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). If you try to remove a non-empty data file using `ALTER DATABASE REMOVE FILE` statement, the error `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` will not be immediately returned. Managed Instance will keep trying to drop the file and the operation will fail after 30min with `Internal server error`.

**Workaround**: Remove the content of the file using `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` command. If this is the only file in the filegroup you would need to delete data from the table or partition associated to this filegroup before you shrink the file, and optionally load this data into another table/partition.

### Change service tier and create instance operations are blocked by ongoing database restore

**Date:** Sep 2019

Ongoing `RESTORE` statement, Data Migration Service migration process, and built-in point-in time restore will block updating service tier or resize of the existing instance and creating new instances until restore process finishes. 
Restore process will block these operations on the Managed instances and instance pools in the same subnet where restore process is running. The instances in instance pools are not affected. Create or change service tier operations will not fail or timeout - they will proceed once the restore process is completed or canceled.

**Workaround**: Wait until the restore process finishes, or cancel the restore process if creation or update service-tier operation has higher priority.

### Missing validations in restore process

**Date:** Sep 2019

`RESTORE` statement and built-in point-in time restore do not perform some nessecary checks on restored database:
- **DBCC CHECKDB** - `RESTORE` statement don't perform `DBCC CHECKDB` on the restored database. If an original database is corrupted, or backup file is corrupted while it is copied to Azure blob Storage, automatic backups will not be taken and Azure support will contact the customer. 
- Built-in Point-in-time restore process doesn't check does the automated backup from Business Critical instance contain the [In-memory OLTP objects](sql-database-in-memory.md#in-memory-oltp). 

**Workaround**: Make sure that you are executing `DBCC CHECKDB` on the source database before taking a backup, and using `WITH CHECKSUM` option in backup to avoid potential corruptions that could be restored on Managed instance. Make sure that your source database doesn't contain [In-memory OLTP objects](sql-database-in-memory.md#in-memory-oltp) if you are restoring it on General Purpose tier.

### Resource Governor on Business Critical service tier might need to be reconfigured after failover

**Date:** Sep 2019

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) feature that enables you to limit the resources assigned to the user workload might incorrectly classify some user workload after failover or user-initiated change of service tier (for example, the change of max vCore or max instance storage size).

**Workaround**: Run `ALTER RESOURCE GOVERNOR RECONFIGURE` periodically or as part of SQL Agent Job that executes the SQL task when the instance starts if you are using 
[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor).

### Cannot authenticate to external mail servers using secure connection (SSL)

**Date:** Aug 2019

Database mail that is [configured using secure connection (SSL)](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-database-mail) cannot authenticate on some email servers outside the Azure. This is security configuration issue that will be resolved soon.

**Workaround:** Temporary remove secure connection (SSL) from the database mail configuration until the issue gets resolved. 

### Cross-database Service Broker dialogs must be re-initialized after service tier upgrade

**Date:** Aug 2019

Cross-database Service Broker dialogs will stop delivering the messages to the services in other databases after change service tier operation. The messages are **not lost** and they can be found in the sender queue. Any change of vCores or instance storage size in Managed Instance, will cause `service_broke_guid` value in [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) view to be changed for all databases. Any `DIALOG` created using [BEGIN DIALOG](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql) statement that references Service Brokers in other database will stop delivering messages to the target service.

**Workaround:** Stop any activity that uses cross-database Service Broker dialog conversations before updating service tier and re-initialize them after. If there are remaining messages that are undelivered after service tier change, read the messages from the source queue and resend them to the target queue.

### Impersonification of AAD login types is not supported

**Date:** July 2019

Impersonation using `EXECUTE AS USER` or `EXECUTE AS LOGIN` of following AAD principals is not supported:
-	Aliased AAD users. The following error is returned in this case `15517`.
- AAD logins and users based on AAD applications or service principals. The following errors are returned in this case `15517` and `15406`.

### @query parameter not supported in sp_send_db_mail

**Date:** April 2019

The `@query` parameter in the [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedure doesn't work.

### Transactional Replication must be reconfigured after geo-failover

**Date:** Mar 2019

If Transactional Replication is enabled on a database in an auto-failover group, the managed instance administrator must clean up all publications on the old primary and reconfigure them on the new primary after a failover to another region occurs. See [Replication](#replication) for more details.

### AAD logins and users are not supported in tools

**Date:** Jan 2019

SQL Server Management Studio and SQL Server Data Tools don't fully support Azure Active directory logins and users.
- Using Azure AD server principals (logins) and users (public preview) with SQL Server Data Tools currently isn't supported.
- Scripting for Azure AD server principals (logins) and users (public preview) isn't supported in SQL Server Management Studio.

### Temporary database is used during RESTORE operation

When a database is restoring on Managed Instance, the restore service will first create an empty database with the desired name to allocate the name on the instance. After some time, this database will be dropped and restoring of the actual database will be started. The database that is in *Restoring* state will temporary have a random GUID value instead of name. The temporary name will be changed to the desired name specified in `RESTORE` statement once the restore process completes. In the initial phase, user can access the empty database and even create tables or load data in this database. This temporary database will be dropped when the restore service starts the second phase.

**Workaround**: Do not access the database that you are restoring until you see that restore is completed.

### TEMPDB structure and content is re-created

The `tempdb` database is always split into 12 data files and the file structure cannot be changed. The maximum size per file can't be changed, and new files cannot be added to `tempdb`. `Tempdb` is always re-created as an empty database when the instance starts or fails over, and any changes made in `tempdb` will not be preserved.

### Exceeding storage space with small database files

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, and `RESTORE DATABASE` statements might fail because the instance can reach the Azure Storage limit.

Each General Purpose managed instance has up to 35 TB of storage reserved for Azure Premium Disk space. Each database file is placed on a separate physical disk. Disk sizes can be 128 GB, 256 GB, 512 GB, 1 TB, or 4 TB. Unused space on the disk isn't charged, but the total sum of Azure Premium Disk sizes can't exceed 35 TB. In some cases, a managed instance that doesn't need 8 TB in total might exceed the 35 TB Azure limit on storage size due to internal fragmentation.

For example, a General Purpose managed instance might have one large file that's 1.2 TB in size placed on a 4-TB disk. It also might have 248 files with 1 GB size each that are placed on separate 128-GB disks. In this example:

- The total allocated disk storage size is 1 x 4 TB + 248 x 128 GB = 35 TB.
- The total reserved space for databases on the instance is 1 x 1.2 TB + 248 x 1 GB = 1.4 TB.

This example illustrates that under certain circumstances, due to a specific distribution of files, a managed instance might reach the 35-TB limit that's reserved for an attached Azure Premium Disk when you might not expect it to.

In this example, existing databases continue to work and can grow without any problem as long as new files aren't added. New databases can't be created or restored because there isn't enough space for new disk drives, even if the total size of all databases doesn't reach the instance size limit. The error that's returned in that case isn't clear.

You can [identify the number of remaining files](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) by using system views. If you reach this limit, try to [empty and delete some of the smaller files by using the DBCC SHRINKFILE statement](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) or switch to the [Business Critical tier, which doesn't have this limit](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### GUID values shown instead of database names

Several system views, performance counters, error messages, XEvents, and error log entries display GUID database identifiers instead of the actual database names. Don't rely on these GUID identifiers because they're replaced with actual database names in the future.

### Error logs aren't persisted

Error logs that are available in managed instance aren't persisted, and their size isn't included in the maximum storage limit. Error logs might be automatically erased if failover occurs. There might be gaps in the error log history because Managed Instance was moved several times on several virtual machines.

### Transaction scope on two databases within the same instance isn't supported

The `TransactionScope` class in .NET doesn't work if two queries are sent to two databases within the same instance under the same transaction scope:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Although this code works with data within the same instance, it required MSDTC.

**Workaround:** Use [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) to use another database in a connection context instead of using two connections.

### CLR modules and linked servers sometimes can't reference a local IP address

CLR modules placed in a managed instance and linked servers or distributed queries that reference a current instance sometimes can't resolve the IP of a local instance. This error is a transient issue.

**Workaround:** Use context connections in a CLR module if possible.

## Next steps

- For more information about managed instances, see [What is a managed instance?](sql-database-managed-instance.md)
- For a features and comparison list, see [Azure SQL Database feature comparison](sql-database-features.md).
- For a quickstart that shows you how to create a new managed instance, see [Create a managed instance](sql-database-managed-instance-get-started.md).
