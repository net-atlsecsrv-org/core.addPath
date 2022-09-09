---
title: 'Quickstart: Configure workload isolation - T-SQL'
description: Use T-SQL to configure workload isolation.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
---

# Quickstart: Configure workload isolation using T-SQL

In this quickstart, you'll quickly create a workload group and classifier for reserving resources for data loading. The workload group will allocate 20% of the system resources to a data loads.  The workload classifier will assign requests to the data loads workload group.  With 20% isolation for data loads, they are guaranteed resources to hit SLAs.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

> [!NOTE]
> Creating a SQL Analytics instance in Azure Synapse Analytics may result in a new billable service.  For more information, see [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## Prerequisites
 
This quickstart assumes you already have a SQL Analytics instance in Azure Synapse and that you have CONTROL DATABASE permissions. If you need to create one, use [Create and Connect - portal](create-data-warehouse-portal.md) to create a data warehouse called **mySampleDataWarehouse**.

## Sign in to the Azure portal

Sign in to the [Azure portal](https://portal.azure.com/).

## Create login for DataLoads

Create a SQL Server authentication login in the `master` database using [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) for 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## Create user

[Create user](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## Create a workload group
Create a [workload group](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) for DataLoads with 20% isolation.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## Create a workload classifier

Create a [workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) to map ELTLogin to the DataLoads workload group.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## View existing workload groups and classifiers and run-time values

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## Clean up resources

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

You're being charged for data warehouse units and data stored in your data warehouse. These compute and storage resources are billed separately.

- If you want to keep the data in storage, you can pause compute when you aren't using the SQL pool. By pausing compute, you're only charged for data storage. When you're ready to work with the data, resume compute.
- If you want to remove future charges, you can delete the data warehouse.

Follow these steps to clean up resources.

1. Sign in to the [Azure portal](https://portal.azure.com), select on your data warehouse.

    ![Clean up resources](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. To pause compute, select the **Pause** button. When the data warehouse is paused, you see a **Start** button.  To resume compute, select **Start**.

3. To remove the data warehouse so you're not charged for compute or storage, select **Delete**.

4. To remove the SQL server you created, select **mynewserver-20180430.database.windows.net** in the previous image, and then select **Delete**.  Be careful with this deletion, since deleting the server also deletes all databases assigned to the server.

5. To remove the resource group, select **myResourceGroup**, and then select **Delete resource group**.

## Next steps

- You've now created a workload group. Run a few queries as ELTLogin to see how they perform. See [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) to view queries and the workload group assigned.
- For more information about SQL Analytics workload management, see [Workload Management](sql-data-warehouse-workload-management.md) and [Workload Isolation](sql-data-warehouse-workload-isolation.md).
