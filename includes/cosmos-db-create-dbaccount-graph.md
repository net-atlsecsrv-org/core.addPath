---
 title: include file
 description: include file
 services: cosmos-db
 author: SnehaGunda
 ms.service: cosmos-db
 ms.topic: include
 ms.date: 01/27/2020
 ms.author: sngun
 ms.custom: include file
---

1. In a new browser window, sign in to the [Azure portal](https://portal.azure.com/).

2. In the left menu, select **Create a resource**.
   
   ![Create a resource in the Azure portal](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. On the **New** page, select **Databases** > **Azure Cosmos DB**.
   
   ![The Azure portal Databases pane](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > If you don't see Azure Cosmos DB in the list, just type it in the search box at the top of the page, and press _Enter_ key. 

3. On the **Create Azure Cosmos DB Account** page, enter the settings for the new Azure Cosmos DB account. 
 
    Setting|Value|Description
    ---|---|---
    Subscription|Your subscription|Select the Azure subscription that you want to use for this Azure Cosmos DB account. 
    Resource Group|Create new<br><br>Then enter the same name as Account Name|Select **Create new**. Then enter a new resource group name for your account. For simplicity, use the same name as your Azure Cosmos DB account name. 
    Account Name|Enter a unique name|Enter a unique name to identify your Azure Cosmos DB account. Your account URI will be *gremlin.azure.com* appended to your unique account name.<br><br>The account name can use only lowercase letters, numbers, and hyphens (-), and must be between 3 and 31 characters long.
    API|Gremlin (graph)|The API determines the type of account to create. Azure Cosmos DB provides five APIs: Core (SQL) for document databases, Gremlin for graph databases, MongoDB for document databases, Azure Table, and Cassandra. You must create a separate account for each API. <br><br>Select **Gremlin (graph)**, because in this quickstart you are creating a table that works with the Gremlin API. <br><br>[Learn more about the Gremlin API](../articles/cosmos-db/graph-introduction.md).|
    Location|Select the region closest to your users|Select a geographic location to host your Azure Cosmos DB account. Use the location that's closest to your users to give them the fastest access to the data.

    Select **Review+Create**. You can skip the **Network** and **Tags** section. 

    ![The new account page for Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. The account creation takes a few minutes. Wait for the portal to display the **Congratulations! Your Azure Cosmos DB account was created** page.
   
   ![Azure Cosmos DB account created page](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

