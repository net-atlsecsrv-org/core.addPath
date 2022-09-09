---
title: Azure SQL Database feature comparison | Microsoft Docs
description: This article compares the features of SQL Server that are available in different flavors of Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
manager: craigg
ms.date: 05/10/2019
---

# Feature comparison: Azure SQL Database versus SQL Server

Azure SQL Database shares a common code base with SQL Server. The features of SQL Server supported by Azure SQL Database depend on the type of Azure SQL database that you create. With Azure SQL Database, you can create a database as part of a [managed instance](sql-database-managed-instance.md), as a single database, or as part of an elastic pool.

Microsoft continues to add features to Azure SQL Database. Visit the Service Updates webpage for Azure for the newest updates using these filters:

- Filtered to the [SQL Database service](https://azure.microsoft.com/updates/?service=sql-database).
- Filtered to General Availability [(GA) announcements](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) for SQL Database features.

## SQL Server feature support in Azure SQL Database

The following table lists the major features of SQL Server and provides information about whether the feature is partially or fully supported and a link to more information about the feature.

| **SQL Feature** | **Supported by single databases and elastic pools** | **Supported by managed instances** |
| --- | --- | --- |
| [Active geo-replication](sql-database-active-geo-replication.md) | Yes - all service tiers other than hyperscale | No, see [Auto-failover groups(preview)](sql-database-auto-failover-group.md) as an alternative |
| [Auto-failover groups](sql-database-auto-failover-group.md) | Yes - all service tiers other than hyperscale | Yes, in [public preview](sql-database-auto-failover-group.md)|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Yes - see [Cert store](sql-database-always-encrypted.md) and [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Yes - see [Cert store](sql-database-always-encrypted.md) and [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [High availability](sql-database-high-availability.md) is included with every database. Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) | [High availability](sql-database-high-availability.md) is included with every database and [cannot be managed by user](sql-database-managed-instance-transact-sql-information.md#always-on-availability). Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) |
| [Attach a database](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | No | No |
| [Application roles](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Yes | Yes |
| [Auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Yes](sql-database-auditing.md)| [Yes](sql-database-managed-instance-auditing.md), with some [differences](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Automatic backups](sql-database-automated-backups.md) | Yes. Full backups are taken every 7 days, differential 12 hours, and log backups every 5-10 min. | Yes. Full backups are taken every 7 days, differential 12 hours, and log backups every 5-10 min. |
| [Automatic tuning (plan forcing)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Yes](sql-database-automatic-tuning.md)| [Yes](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatic tuning (indexes)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Yes](sql-database-automatic-tuning.md)| No |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Yes | Yes |
| [BACPAC file (export)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes - see [SQL Database export](sql-database-export.md) | Yes - see [SQL Database export](sql-database-export.md) |
| [BACPAC file (import)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes - see [SQL Database import](sql-database-import.md) | Yes - see [SQL Database import](sql-database-import.md) |
| [BACKUP command](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | No, only system-initiated automatic backups - see [Automated backups](sql-database-automated-backups.md) | Yes, user initiated copy-only backups to Azure Blob Storage (automatic system backups cannot be initiated by user) - see [Backup differences](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Built-in functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most - see individual functions | Yes - see [Stored procedures, functions, triggers differences](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK INSERT statement](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | Yes, but just from Azure Blob storage as a source. | Yes, but just from Azure Blob Storage as a source - see [differences](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset). |
| [Certificates and asymmetric keys](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | Yes, without access to file system for `BACKUP` and `CREATE` operations. | Yes, without access to file system for `BACKUP` and `CREATE` operations - see [certificate differences](sql-database-managed-instance-transact-sql-information.md#certificates). | 
| [Change data capture](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | No | Yes |
| [Change tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Yes |Yes |
| [Collation - database](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Yes | Yes |
| [Collation - server/instance](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | No, default logical server collation `SQL_Latin1_General_CP1_CI_AS` is always used. | Yes, can be set when the [instance is created](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) and cannot be updated later. |
| [Columnstore indexes](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Yes - [Premium tier, Standard tier - S3 and above, General Purpose tier, and Business Critical tiers](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Yes |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | No | Yes, but without access to file system in `CREATE ASSEMBLY` statement - see [CLR differences](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Contained databases](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Yes | Currently no [due to defect in RESTORE including point-in-time RESTORE](sql-database-managed-instance-transact-sql-information.md#cant-restore-contained-database). This is a defect that will be fixed soon. |
| [Contained users](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Yes | Yes |
| [Control of flow language keywords](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Yes | Yes |
| [Credentials](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | Yes, but only [database scoped credentials](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql). | Yes, but only **Azure Key Vault** and `SHARED ACCESS SIGNATURE` are supported see [details](sql-database-managed-instance-transact-sql-information.md#credential) |
| [Cross-database queries](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No - see [Elastic queries](sql-database-elastic-query-overview.md) | Yes, plus [Elastic queries](sql-database-elastic-query-overview.md) |
| [Cross-database transactions](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No | Yes, within the instance. See [Linked server differences](sql-database-managed-instance-transact-sql-information.md#linked-servers) for cross-instance queries. |
| [Cursors](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Yes |Yes |
| [Data compression](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Yes |Yes |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | No | Yes |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Yes | Yes |
| [Database mirroring](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | No | [No](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [Database configuration settings](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Yes | Yes |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | No | No |
| [Database snapshots](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | No | No |
| [Data types](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Yes |Yes |
| [DBCC statements](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Most - see individual statements | Yes - see [DBCC differences](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL statements](https://docs.microsoft.com/sql/t-sql/statements/statements) | Most - see individual statements | Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [DDL triggers](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Database only |  Yes |
| [Distributed partition views](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | No | Yes |
| [Distributed transactions - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | No - see [Elastic transactions](sql-database-elastic-transactions-overview.md) |  No - see [Linked server differences](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML statements](https://docs.microsoft.com/sql/t-sql/queries/queries) | Yes | Yes |
| [DML triggers](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Most - see individual statements |  Yes |
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Most - see individual DMVs |  Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [Dynamic data masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Yes](sql-database-dynamic-data-masking-get-started.md)| [Yes](sql-database-dynamic-data-masking-get-started.md) |
| [Elastic pools](sql-database-elastic-pool.md) | Yes | Built-in - a single Managed Instance can have multiple databases that share the same pool of resources |
| [Event notifications](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | No - see [Alerts](sql-database-insights-alerts-portal.md) | No |
| [Expressions](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Yes | Yes |
| [Extended events](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Some - see [Extended events in SQL Database](sql-database-xevent-db-diff-from-svr.md) | Yes - see [Extended events differences](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Extended stored procedures](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | No | No |
| [Files and file groups](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Primary file group only | Yes. File paths are automatically assigned and the file location cannot be specified in `ALTER DATABASE ADD FILE` [statement](sql-database-managed-instance-transact-sql-information.md#alter-database-statement).  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | No | [No](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [Full-text search](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Yes, but third-party word breakers are not supported | Yes, but [third-party word breakers are not supported](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most - see individual functions | Yes - see [Stored procedures, functions, triggers differences](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [Geo-restore](sql-database-recovery-using-backups.md#geo-restore) | Yes - all service tiers other than hyperscale | Yes - using [Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa). |
| [Graph processing](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Yes | Yes |
| [In-memory optimization](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Yes - [Premium and Business Critical tiers only](sql-database-in-memory.md) | Yes - [Business Critical tier only](sql-database-managed-instance.md) |
| [JSON data support](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Yes](sql-database-json-features.md) | [Yes](sql-database-json-features.md) |
| [Language elements](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Most - see individual elements |  Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [Linked servers](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | No - see [Elastic query](sql-database-elastic-query-horizontal-partitioning.md) | Only to [SQL Server and SQL Database](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [Log shipping](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [High availability](sql-database-high-availability.md) is included with every database. Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) |[High availability](sql-database-high-availability.md) is included with every database. Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) |
| [Logins and users](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | Yes, but `CREATE` and `ALTER` login statements do not offer all the options (no Windows and server-level Azure Active Directory logins). `EXECUTE AS LOGIN` is not supported - use `EXECUTE AS USER` instead.  | Yes, with some [differences](sql-database-managed-instance-transact-sql-information.md#logins-and-users). Windows logins are not supported and they should be replaced with Azure Active Directory logins. |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | No | No |
| [Minimal logging in bulk import](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | No | No |
| [Modifying system data](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | No | Yes |
| [OLE Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | No | No |
| [Online index operations](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Yes | Yes |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|No|Yes, only to other Azure SQL Databases and SQL Servers. See [T-SQL differences](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Yes|Yes|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|No|Yes, only to other Azure SQL Databases and SQL Servers. See [T-SQL differences](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Yes, only to import from Azure Blob storage. |Yes, only to other Azure SQL Databases and SQL Servers, and to import from Azure Blob storage. See [T-SQL differences](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Yes|Yes|
| [Operators](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Most - see individual operators |Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [Partitioning](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Yes | Yes |
| Public IP address | Yes. The access can be restricted using firewall or service endpoints.  | Yes. Needs to be explicitly enabled and port 3342 must be enabled in NSG rules. Public IP can be disabled if needed. See [Public endpoint](sql-database-managed-instance-public-endpoint-securely.md) for more details. | 
| [Point in time database restore](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Yes - all service tiers other than hyperscale - see [SQL Database recovery](sql-database-recovery-using-backups.md#point-in-time-restore) | Yes - see [SQL Database recovery](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | No | No |
| [Policy-based management](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | No | No |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Yes | Yes |
| [Query Notifications](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | No | Yes |
| [Query Performance Insights](sql-database-query-performance.md) | Yes | No |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Yes, in [public preview](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | No |
| [Resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | No | Yes |
| [RESTORE statements](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | No | Yes, with mandatory `FROM URL` options for the backups files placed on Azure Blob Storage. See [Restore differences](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restore database from backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | From automated backups only - see [SQL Database recovery](sql-database-recovery-using-backups.md) | From automated backups - see [SQL Database recovery](sql-database-recovery-using-backups.md) and from full backups placed on Azure Blob Storage - see [Backup differences](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Row Level Security](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Yes | Yes |
| [Semantic search](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | No | No |
| [Sequence numbers](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Yes | Yes |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | No | Yes, but only within the instance. See [Service Broker differences](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Server configuration settings](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | No | Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [Set statements](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Most - see individual statements | Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [Yes](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | Yes [version 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Yes | Yes |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Yes | Yes |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Yes | No |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | No - see [Elastic jobs](elastic-jobs-overview.md) | Yes - see [SQL Server Agent differences](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | No, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) is a separate Azure cloud service. | No, [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) is a separate Azure cloud service. |
| [SQL Server Auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | No - see [SQL Database auditing](sql-database-auditing.md) | Yes - see [Auditing differences](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes | Yes |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Yes, with a managed SSIS in Azure Data Factory (ADF) environment, where packages are stored in SSISDB hosted by Azure SQL Database and executed on Azure SSIS Integration Runtime (IR), see [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>To compare the SSIS features in SQL Database server and Managed Instance, see [Compare Azure SQL Database single databases/elastic pools and Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Yes, with a managed SSIS in Azure Data Factory (ADF) environment, where packages are stored in SSISDB hosted by Managed Instance and executed on Azure SSIS Integration Runtime (IR), see [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>To compare the SSIS features in SQL Database and Managed Instance, see [Compare Azure SQL Database single databases/elastic pools and Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Yes | Yes [version 18.0 and higher](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Yes | Yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | No - see [Extended events](sql-database-xevent-db-diff-from-svr.md) | Yes |
| [SQL Server Replication](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Transactional and snapshot replication subscriber only](sql-database-single-database-migrate.md) | Yes, in [public preview](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | No - [see Power BI](https://docs.microsoft.com/power-bi/) | No - [see Power BI](https://docs.microsoft.com/power-bi/) |
| [Stored procedures](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Yes | Yes |
| [System stored functions](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Most - see individual functions | Yes - see [Stored procedures, functions, triggers differences](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [System stored procedures](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Some - see individual stored procedures | Yes - see [Stored procedures, functions, triggers differences](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [System tables](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Some - see individual tables | Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [System catalog views](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Some - see individual views | Yes - see [T-SQL differences](sql-database-managed-instance-transact-sql-information.md) |
| [Temporary tables](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Local and database-scoped global temporary tables | Local and instance-scoped global temporary tables |
| [Temporal tables](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Yes](sql-database-temporal-tables.md) | [Yes](sql-database-temporal-tables.md) |
| Time zone choice | No | [Yes(preview)](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone) |
| Threat detection|  [Yes](sql-database-threat-detection.md)|[Yes](sql-database-managed-instance-threat-detection.md)|
| [Trace flags](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | No | No |
| [Variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Yes | Yes |
| [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Yes - General Purpose and Business Critical service tiers only| [Yes](transparent-data-encryption-azure-sql.md) |
| [VNet](../virtual-network/virtual-networks-overview.md) | Partial - see [VNet Endpoints](sql-database-vnet-service-endpoint-rule-overview.md) | Yes, Resource Manager model only |
| [Windows Server Failover Clustering](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [High availability](sql-database-high-availability.md) is included with every database. Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) | [High availability](sql-database-high-availability.md) is included with every database. Disaster recovery is discussed in [Overview of business continuity with Azure SQL Database](sql-database-business-continuity.md) |
| [XML indexes](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Yes | Yes |

## Next steps

- For information about the Azure SQL Database service, see [What is SQL Database?](sql-database-technical-overview.md)
- For information about a Managed Instance, see [What is a Managed Instance?](sql-database-managed-instance.md).
