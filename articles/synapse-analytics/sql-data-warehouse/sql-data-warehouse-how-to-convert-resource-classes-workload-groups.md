---
title: Convert resource class to a workload group 
description: Learn how to create a workload group that is similar to a resource class in Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: 
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
---

# Convert Resource Classes to Workload Groups

Workload groups provide a mechanism to isolate and contain system resources.  Additionally, workload groups allow you to set execution rules for the requests running in them.  A query timeout execution rule allows runaway queries to be canceled without user intervention.  This article explains how to take an existing resource class and create a workload group with a similar configuration.  In addition, an optional query timeout rule is added.

## Understanding the existing resource class configuration

Workload groups require a parameter called `REQUEST_MIN_RESOURCE_GRANT_PERCENT` that specifies the percentage of overall system resources allocated per request.  Resource allocation is done for [resource classes](resource-classes-for-workload-management.md#what-are-resource-classes) by allocating concurrency slots.  To determine the value to specify for `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, use the sys.dm_workload_management_workload_groups_stats <link tbd> DMV.  For example, the below query query returns a value that can be used for the `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parameter to create a workload group similar to staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Workload groups operate based on percentage of overall system resources.  

Because workload groups operate based on percentage of overall system resources, as you scale up and down, the percentage of resources allocated to static resource classes relative to the overall system resources changes.  For example, staticrc40 at DW1000c allocates 9.6% of the overall system resources.  At DW2000c, 19.2% are allocated.  This model is similar if you wish to scale up for concurrency versus allocating more resources per request.

## Create Workload Group

With the known `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, you can use the CREATE WORKLOAD GROUP <link> syntax to create the workload group.  You can optionally specify a `MIN_PERCENTAGE_RESOURCE` that is greater than zero to isolate resources for the workload group.  Also, you can optionally specify `CAP_PERCENTAGE_RESOURCE` less than 100 to limit the amount of resources the workload group can consume.  

The below example sets the `MIN_PERCENTAGE_RESOURCE` to dedicate 9.6% of the system resources to `wgDataLoads` and guarantees one query will be able to run all the times.  Additionally, `CAP_PERCENTAGE_RESOURCE` is set to 38.4% and limits this workload group to four concurrent requests.  By setting the `QUERY_EXECUTION_TIMEOUT_SEC` parameter to 3600, any query that runs for more than 1 hour will be automatically canceled.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## Create the Classifier

Previously, the mapping of queries to resource classes was done with [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  To achieve the same functionality and map requests to workload groups, use the [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) syntax.  Using sp_addrolemember only allowed you to map resources to a request based on a login.  A classifier provides additional options besides login, such as:
    - label
    - session
    - time
The below example assigns queries from the `AdfLogin` login that also have the [OPTION LABEL](sql-data-warehouse-develop-label.md)  set to `factloads` to the workload group `wgDataLoads` created above.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## Test with a sample query

Below is a sample query and a DMV query to ensure the workload group and classifier are configured correctly.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## Next steps

- [Workload Isolation](sql-data-warehouse-workload-isolation.md)
- [How-To Create a Workload Group-link](quickstart-configure-workload-isolation-tsql.md)
