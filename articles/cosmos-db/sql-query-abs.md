---
title: ABS in Azure Cosmos DB query language
description: Learn about SQL system function ABS in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
---
# ABS (Azure Cosmos DB)
 Returns the absolute (positive) value of the specified numeric expression.  
  
## Syntax
  
```sql
ABS (<numeric_expr>)  
```  
  
## Arguments
  
*numeric_expr*  
   Is a numeric expression.  
  
## Return types
  
  Returns a numeric expression.  
  
## Examples
  
  The following example shows the results of using the `ABS` function on three different numbers.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Here is the result set.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## Next steps

- [Mathematical functions Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System functions Azure Cosmos DB](sql-query-system-functions.md)
- [Introduction to Azure Cosmos DB](introduction.md)
