---
title: Collation 
description: Collation types supported in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: antvgski 
manager: igorstan
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
---

# Database collation support for Azure SQL Data Warehouse

You can change the default database collation from the Azure portal when you create a new Azure SQL Data Warehouse database. This capability makes it even easier to create a new database using one of the 3800 supported database collations for SQL Data Warehouse.
Collations provide the locale, code page, sort order and character sensitivity rules for character-based data types. Once chosen, all columns and expressions requiring collation information inherit the chosen collation from the database setting. The default inheritance can be overridden by explicitly stating a different collation for a character-based data type.

## Changing collation
To change the default collation, you simple update to the Collation field in the provisioning experience.

For example, if you wanted to change the default collation to case sensitive, you would simply rename the Collation from SQL_Latin1_General_CP1_CI_AS to SQL_Latin1_General_CP1_CS_AS. 

## List of unsupported collation types
*	Japanese_Bushu_Kakusu_140_BIN
*	Japanese_Bushu_Kakusu_140_BIN2
*	Japanese_Bushu_Kakusu_140_CI_AI_VSS
*	Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AI_VSS
*	Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*	Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*	Japanese_Bushu_Kakusu_140_CI_AI
*	Japanese_Bushu_Kakusu_140_CI_AI_WS
*	Japanese_Bushu_Kakusu_140_CI_AI_KS
*	Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*	Japanese_Bushu_Kakusu_140_CI_AS
*	Japanese_Bushu_Kakusu_140_CI_AS_WS
*	Japanese_Bushu_Kakusu_140_CI_AS_KS
*	Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*	Japanese_Bushu_Kakusu_140_CS_AI
*	Japanese_Bushu_Kakusu_140_CS_AI_WS
*	Japanese_Bushu_Kakusu_140_CS_AI_KS
*	Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*	Japanese_Bushu_Kakusu_140_CS_AS
*	Japanese_Bushu_Kakusu_140_CS_AS_WS
*	Japanese_Bushu_Kakusu_140_CS_AS_KS
*	Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*	Japanese_XJIS_140_BIN
*	Japanese_XJIS_140_BIN2
*	Japanese_XJIS_140_CI_AI_VSS
*	Japanese_XJIS_140_CI_AI_WS_VSS
*	Japanese_XJIS_140_CI_AI_KS_VSS
*	Japanese_XJIS_140_CI_AI_KS_WS_VSS
*	Japanese_XJIS_140_CI_AS_VSS
*	Japanese_XJIS_140_CI_AS_WS_VSS
*	Japanese_XJIS_140_CI_AS_KS_VSS
*	Japanese_XJIS_140_CI_AS_KS_WS_VSS
*	Japanese_XJIS_140_CS_AI_VSS
*	Japanese_XJIS_140_CS_AI_WS_VSS
*	Japanese_XJIS_140_CS_AI_KS_VSS
*	Japanese_XJIS_140_CS_AI_KS_WS_VSS
*	Japanese_XJIS_140_CS_AS_VSS
*	Japanese_XJIS_140_CS_AS_WS_VSS
*	Japanese_XJIS_140_CS_AS_KS_VSS
*	Japanese_XJIS_140_CS_AS_KS_WS_VSS
*	Japanese_XJIS_140_CI_AI
*	Japanese_XJIS_140_CI_AI_WS
*	Japanese_XJIS_140_CI_AI_KS
*	Japanese_XJIS_140_CI_AI_KS_WS
*	Japanese_XJIS_140_CI_AS
*	Japanese_XJIS_140_CI_AS_WS
*	Japanese_XJIS_140_CI_AS_KS
*	Japanese_XJIS_140_CI_AS_KS_WS
*	Japanese_XJIS_140_CS_AI
*	Japanese_XJIS_140_CS_AI_WS
*	Japanese_XJIS_140_CS_AI_KS
*	Japanese_XJIS_140_CS_AI_KS_WS
*	Japanese_XJIS_140_CS_AS
*	Japanese_XJIS_140_CS_AS_WS
*	Japanese_XJIS_140_CS_AS_KS
*	Japanese_XJIS_140_CS_AS_KS_WS
*	SQL_EBCDIC1141_CP1_CS_AS
*	SQL_EBCDIC277_2_CP1_CS_AS

## Checking the current collation
To check the current collation for the database, you can run the following T-SQL snippet:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
When passed ‘Collation’ as the property parameter, the DatabasePropertyEx function returns the current collation for the database specified. You can learn more about the DatabasePropertyEx function on MSDN.

