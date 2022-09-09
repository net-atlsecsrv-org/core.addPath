---
title: ASIN in Azure Cosmos DB query language
description: Learn about SQL system function ASIN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
---
# ASIN (Azure Cosmos DB)
 Returns the angle, in radians, whose sine is the specified numeric expression. This is also called arcsine.  
  
## Syntax
  
```sql
ASIN(<numeric_expr>)  
```  
  
## Arguments
  
*numeric_expr*  
   Is a numeric expression.  
  
## Return types
  
  Returns a numeric expression.  
  
## Examples
  
  The following example returns the `ASIN` of -1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Here is the result set.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## Next steps

- [Mathematical functions Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System functions Azure Cosmos DB](sql-query-system-functions.md)
- [Introduction to Azure Cosmos DB](introduction.md)
