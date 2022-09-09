---
 title: include file
 description: include file
 services: cosmos-db
 author: SnehaGunda
 ms.service: cosmos-db
 ms.topic: include
 ms.date: 04/05/2019
 ms.author: sngun
 ms.custom: include file
---
You can use queries in Data Explorer to retrieve and filter your data.

1. At the top of the **Items** tab in Data Explorer, review the default query `SELECT * FROM c`. This query retrieves and displays all documents from the container ordered by ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Default query in Data Explorer is SELECT * FROM c":::
   
1. To change the query, select **Edit Filter**, replace the default query with `ORDER BY c._ts DESC`, and then select **Apply Filter**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Change the default query by adding ORDER BY c._ts DESC and clicking Apply Filter":::

   The modified query displays the documents in descending order based on their time stamp, so now your second document is listed first. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Changed query to ORDER BY c._ts DESC and clicking Apply Filter":::

If you're familiar with SQL syntax, you can enter any supported [SQL queries](../articles/cosmos-db/sql-api-sql-query.md) in the query predicate box. You can also use Data Explorer to create stored procedures, UDFs, and triggers for server-side business logic. 

Data Explorer provides easy Azure portal access to all of the built-in programmatic data access features available in the APIs. You also use the portal to scale throughput, get keys and connection strings, and review metrics and SLAs for your Azure Cosmos DB account. 

