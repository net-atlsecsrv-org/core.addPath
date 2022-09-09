---
title: SSIS migration with Azure SQL Database managed instance as the database workload destination 
description: SSIS migration with Azure SQL Database managed instance as the database workload destination.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: 
manager: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
---
# SSIS migration with Azure SQL Database managed instance as the database workload destination

When migrating database workloads from SQL Server on premises to Azure SQL Database managed instance, you should be familiar with [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS), and the [network topologies for Azure SQL Database managed instance migrations using DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

This article focuses on the migration of SQL Server Integration Service (SSIS) packages stored in SSIS catalog (SSISDB) and SQL Server Agent jobs that schedule SSIS package executions.

## Migrate SSIS catalog (SSISDB)

SSISDB migration can be done using DMS, as described in the article:
[Migrate SSIS packages to an Azure SQL Database managed instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## SSIS jobs to Azure SQL Database managed instance agent

Azure SQL Database managed instance has a native, first-class scheduler just like SQL Server Agent on premises.  Since a migration tool for SSIS jobs is not yet available, they have to be migrated from SQL Server Agent on premises to Azure SQL Database managed instance agent via scripts/manual copy.

## Additional resources

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Network topologies for Azure SQL Database managed instance migrations using DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrate SSIS packages to an Azure SQL Database managed instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## Next steps

- [Connect to SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Run SSIS packages deployed in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
