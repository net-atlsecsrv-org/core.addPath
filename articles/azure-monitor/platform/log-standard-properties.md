---
title: Standard properties in Azure Monitor log records | Microsoft Docs
description: Describes properties that are common to multiple data types in Azure Monitor logs.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
---

# Standard properties in Azure Monitor log records
Log data in Azure Monitor is [stored as a set of records](../log-query/log-query-overview.md), each with a particular data type that has a unique set of properties. Many data types will have standard properties that are common across multiple types. This article describes these properties and provides examples of how you can use them in queries.

Some of these properties are still in the process of being implemented, so you may see them in some data types but not yet in others.

## TimeGenerated
The **TimeGenerated** property contains the date and time that the record was created. It provides a common property to use for filtering or summarizing by time. When you select a time range for a view or dashboard in the Azure portal, it uses TimeGenerated to filter the results.

### Examples

The following query returns the number of error events created for each day in the previous week.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## Type
The **Type** property holds the name of the table that the record was retrieved from which can also be thought of as the record type. This property is useful in queries that combine records from multiple table, such as those that use the `search` operator, to distinguish between records of different types. **$table** can be used in place of **Type** in some places.

### Examples
The following query returns the count of records by type collected over the past hour.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## \_ResourceId
The **\_ResourceId** property holds a unique identifier for the resource that the record is associated with. This gives you a standard property to use to scope your query to only records from a particular resource, or to join related data across multiple tables.

For Azure resources, the value of **_ResourceId** is the [Azure resource ID URL](../../azure-resource-manager/resource-group-template-functions-resource.md). The property is currently limited to Azure resources, but it will be extended to resources outside of Azure such as on-premises computers.

> [!NOTE]
> Some data types already have fields that contain Azure resource ID or at least parts of it like subscription ID. While these fields are kept for backward compatibility, it is recommended to use the _ResourceId to perform cross correlation since it will be more consistent.

### Examples
The following query joins performance and event data for each computer. It shows all events with an ID of _101_ and processor utilization over 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

The following query joins _AzureActivity_ records with _SecurityEvent_ records. It shows all activity operations with users that were logged in to these machines.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

The following query parses **_ResourceId** and aggregates billed data volumes per Azure subscription.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Use these `union withsource = tt *` queries sparingly as scans across data types are expensive to execute.

## \_IsBillable
The **\_IsBillable** property specifies whether ingested data is billable. Data with **\_IsBillable** equal to _false_ are collected for free and not billed to your Azure account.

### Examples
To get a list of computers sending billed data types, use the following query:

> [!NOTE]
> Use queries with `union withsource = tt *` sparingly as scans across data types are expensive to execute. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

This can be extended to return the count of computers per hour that are sending billed data types:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## \_BilledSize
The **\_BilledSize** property specifies the size in bytes of data that will be billed to your Azure account if **\_IsBillable** is true.

### Examples
To see the size of billable events ingested per computer, use the `_BilledSize` property which provides the size in bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

To see the count of events ingested per computer, use the following query:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

To see the count of billable events ingested per computer, use the following query: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

If you want to see counts for billable data types are sending data to a specific computer, use the following query:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## Next steps

- Read more about how [Azure Monitor log data is stored](../log-query/log-query-overview.md).
- Get a lesson on [writing log queries](../../azure-monitor/log-query/get-started-queries.md).
- Get a lesson on [joining tables in log queries](../../azure-monitor/log-query/joins.md).
