---
title: "Azure Synapse Analytics: Migration guide"
description: Follow this guide to migrate your databases to Azure Synapse Analytics dedicated SQL pool. 
ms.service: synapse-analytics
ms.subservice:
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
---
# Migrating a data warehouse to a dedicated SQL pool in Azure Synapse Analytics 
The following sections provide an overview of what's involved with migrating an existing data warehouse solution to Azure Synapse Analytics dedicated SQL pool.

## Overview
Before migrating, you should verify that Azure Synapse Analytics is the best solution for your workload. Azure Synapse Analytics is a distributed system designed to perform analytics on large data. Migrating to Azure Synapse Analytics requires some design changes that are not difficult to understand but that might take some time to implement. If your business requires an enterprise-class data warehouse, the benefits are worth the effort. However, if you don't need the power of Azure Synapse Analytics, it is more cost-effective to use [SQL Server](https://docs.microsoft.com/sql/sql-server/) or [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Consider using Azure Synapse Analytics when you:
- Have one or more Terabytes of data.
- Plan to run analytics on substantial amounts of data.
- Need the ability to scale compute and storage.
- Want to save on costs by pausing compute resources when you don't need them.

Rather than Azure Synapse Analytics, consider other options for operational (OLTP) workloads that have:
- High frequency reads and writes.
- Large numbers of singleton selects.
- High volumes of single row inserts.
- Row-by-row processing needs.
- Incompatible formats (JSON, XML).

## Azure Synapse Pathway
One of the critical blockers customers face is translating their database objects when migrating from one system to another. [Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) helps you upgrade to a modern data warehouse platform by automating the object translation of your existing data warehouse. It's a free, intuitive, and easy to use tool that automates the code translation enabling a quicker migration to Azure Synapse Analytics.

## Prerequisites
# [Migrate from SQL Server](#tab/migratefromSQLServer)
To migrate your SQL Server data warehouse to Azure Synapse Analytics, make sure you have the following prerequisites: 

- A data warehouse or Analytics workload 
- Download the latest [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) tool to migrate SQL Server objects to Azure Synapse objects.
- A [dedicated SQL pool](../get-started-create-workspace.md) in Azure Synapse workspace. 

# [Migrate from Netezza](#tab/migratefromNetezza)
To migrate your Netezza data warehouse to Azure Synapse Analytics, make sure you have the following prerequisites: 

- Download the latest [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) tool to migrate SQL Server objects to Azure Synapse objects.
- A [dedicated SQL pool](../get-started-create-workspace.md) in Azure Synapse workspace.

For more information visit [Azure Synapse Analytics solutions and migration for Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# [Migrate from Snowflake](#tab/migratefromSnowflake)
To migrate your Snowflake data warehouse to Azure Synapse Analytics, make sure you have the following prerequisites: 

- Download the latest [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) tool to migrate Snowflake objects to Azure Synapse objects.
- A [dedicated SQL pool](../get-started-create-workspace.md) in Azure Synapse workspace. 

# [Migrate from Oracle](#tab/migratefromOracle)
To migrate your Oracle data warehouse to Azure Synapse Analytics, make sure you have the following prerequisites: 

- A data warehouse or Analytics workload 
- Download SSMA for Oracle to convert Oracle objects to SQL Server. See [Migrating Oracle Databases to SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) for more information. 
- Download the latest version of [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=102787) tool to migrate SQL Server objects to Azure Synapse objects.
- A [dedicated SQL pool](../get-started-create-workspace.md) in Azure Synapse workspace. 

For more information visit [Azure Synapse Analytics solutions and migration for an Oracle data warehouse](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## Pre-migration
After you make the decision to migrate an existing solution to Azure Synapse Analytics, it is important to plan the migration before you get started. A primary goal of planning is to ensure that your data, table schemas, and code are compatible with Azure Synapse Analytics. There are some compatibility differences between your current system and Azure Synapse Analytics that you will need to work around. In addition, migrating large amounts of data to Azure takes time. Careful planning will speed up the process of getting your data to Azure. Another key goal of planning is to adjust your design to ensure that your solution takes full advantage of the high query performance that Azure Synapse Analytics is designed to provide. Designing data warehouses for scale introduces unique design patterns, so traditional approaches aren't always the best. While some design adjustments can be made after migration, making changes earlier in the process will save you time later.

## Migrate
Performing a successful migration requires you to migrate your table schemas, code, and data. For more detailed guidance on these topics, see:
- The article [Consider table design](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- The article [Consider code change](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- The article [Migrate your data](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- The article [Consider Workload Management](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## Additional resources 
- The CAT (Customer Advisory Team) has some great Azure Synapse Analytics (formerly SQL DW) guidance published as blog postings. Be sure to take a look at their article, [Migrating data to Azure SQL Data Warehouse in practice](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), for additional guidance on migration.

## Migration assets from real-world engagements
For additional assistance with completing this migration scenario, please see the following resources, which were developed in support of a real-world migration project engagement.

| Title/link                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | This tool provides suggested “best fit” target platforms, cloud readiness, and application/database remediation level for a given workload. It offers simple, one-click calculation and report generation that greatly helps to accelerate large estate assessments by providing and automated and uniform target platform decision process. |
| [Handling Data Encoding Issues While Loading Data to Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | This blog is intended to provide insight on some of the data encoding issues that you may encounter while using PolyBase to load data to SQL Data Warehouse. This article also provides some options that you can use to overcome such issues and load the data successfully. |
| [Getting table sizes in Azure Synapse Analytics dedicated SQL pool](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | One of the key tasks that an architect must perform execute is to get metrics about a new environment post-migration: collecting load times from on-premises to the cloud, collecting PolyBase load times, etc. Of these tasks, one of the most important is to determine the storage size in SQL Data Warehouse compared to the customer's current platform. |
| [Utility to move On-Premises SQL Server Logins to Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | A PowerShell script that creates a T-SQL command script to re-create logins and select database users from an “on premises” SQL Server to an Azure SQL PaaS service. The tool allows the automatic mapping of Windows AD accounts to Azure AD accounts or it can do UPN lookups for each login against the on premises Windows Active Directory. The tool optionally moves SQL Server native logins as well. Custom server and database roles are scripted, as well as role membership and database role and user permissions. Contained databases are yet not supported and only a subset of possible SQL Server permissions are scripted; i.e. permissions grant with grant are not supported (complex permission trees). More details are available in the support document and the script have comments for ease of understanding. |

The Data SQL Engineering team developed these resources. This team's core charter is to unblock and accelerate complex modernization for data platform migration projects to Microsoft's Azure data platform.

## Videos
- Watch how [Walgreens migrated their retail inventory system](https://www.youtube.com/watch?v=86dhd8N1lH4) with about 100TB of data from Netezza to Azure Synapse Analytics (formerly SQL DW) in record time. 