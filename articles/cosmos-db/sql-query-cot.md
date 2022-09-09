---
title: COT in Azure Cosmos DB query language
description: Learn about SQL system function COT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
---
# COT (Azure Cosmos DB)
 Returns the trigonometric cotangent of the specified angle, in radians, in the specified numeric expression.  
  
## Syntax
  
```sql
COT(<numeric_expr>)  
```  
  
## Arguments
  
*numeric_expr*  
   Is a numeric expression.  
  
## Return types
  
  Returns a numeric expression.  
  
## Examples
  
  The following example calculates the `COT` of the specified angle.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Here is the result set.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## Next steps

- [Mathematical functions Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System functions Azure Cosmos DB](sql-query-system-functions.md)
- [Introduction to Azure Cosmos DB](introduction.md)
