---
title: Build an Azure Cosmos DB .NET Framework, Core application using the Gremlin API
description: Presents a .NET Framework/Core code sample you can use to connect to and query Azure Cosmos DB
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: jasonh
ms.custom: devx-track-dotnet

---
# Quickstart: Build a .NET Framework or Core application using the Azure Cosmos DB Gremlin API account

> [!div class="op_single_selector"]
> * [Gremlin console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB is Microsoft's globally distributed multi-model database service. You can quickly create and query document, key/value, and graph databases, all of which benefit from the global distribution and horizontal scale capabilities at the core of Azure Cosmos DB. 

This quickstart demonstrates how to create an Azure Cosmos DB [Gremlin API](graph-introduction.md) account, database, and graph (container) using the Azure portal. You then build and run a console app built using the open-source driver [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet).  

## Prerequisites

If you don't already have Visual Studio 2019 installed, you can download and use the **free** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Make sure that you enable **Azure development** during the Visual Studio setup.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create a database account

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## Add a graph

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## Clone the sample application

Now let's clone a Gremlin API app from GitHub, set the connection string, and run it. You'll see how easy it is to work with data programmatically. 

1. Open a command prompt, create a new folder named git-samples, then close the command prompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open a git terminal window, such as git bash, and use the `cd` command to change to the new folder to install the sample app.

    ```bash
    cd "C:\git-samples"
    ```

3. Run the following command to clone the sample repository. This command creates a copy of the sample app on your computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Then open Visual Studio and open the solution file.

5. Restore the NuGet packages in the project. This should include the Gremlin.Net driver, as well as the Newtonsoft.Json package.


6. You can also install the Gremlin.Net driver manually using the Nuget package manager, or the [nuget command-line utility](https://docs.microsoft.com/nuget/install-nuget-client-tools): 

    ```bash
    nuget install Gremlin.Net
    ```

## Review the code

This step is optional. If you're interested in learning how the database resources are created in the code, you can review the following snippets. Otherwise, you can skip ahead to [Update your connection string](#update-your-connection-string). 

The following snippets are all taken from the Program.cs file.

* Set your connection parameters based on the account created above: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* The Gremlin commands to be executed are listed in a Dictionary:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Create a new `GremlinServer` and `GremlinClient` connection objects using the parameters provided above:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Execute each Gremlin query using the `GremlinClient` object with an async task. You can read the Gremlin queries from the dictionary defined in the previous step and execute them. Later get the result and read the values, which are formatted as a dictionary, using the `JsonSerializer` class from Newtonsoft.Json package:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## Update your connection string

Now go back to the Azure portal to get your connection string information and copy it into the app.

1. From the [Azure portal](https://portal.azure.com/), navigate to your graph database account. In the **Overview** tab, you can see two endpoints- 
 
   **.NET SDK URI** - This value is used when you connect to the graph account by using Microsoft.Azure.Graphs library. 

   **Gremlin Endpoint** - This value is used when you connect to the graph account by using Gremlin.Net library.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="Copy the endpoint":::

   To run this sample, copy the **Gremlin Endpoint** value, delete the port number at the end, that is the URI becomes `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. The endpoint value should look like `testgraphacct.gremlin.cosmosdb.azure.com`

1. Next, navigate to the **Keys** tab and copy the **PRIMARY KEY** value from the Azure portal. 

1. After you have copied the URI and PRIMARY KEY of your account, save them to a new environment variable on the local machine running the application. To set the environment variable, open a command prompt window, and run the following command. Make sure to replace <Your_Azure_Cosmos_account_URI> and <Your_Azure_Cosmos_account_PRIMARY_KEY> values.

   ```console
   setx EndpointUrl "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Open the *Program.cs* file and update the "database and "container" variables with the database and container (which is also the graph name) names created above.

    `private static string database = "your-database-name";`
    `private static string container = "your-container-or-graph-name";`

1. Save the Program.cs file. 

You've now updated your app with all the info it needs to communicate with Azure Cosmos DB. 

## Run the console app

Click CTRL + F5 to run the application. The application will print both the Gremlin query commands and results in the console.

   The console window displays the vertexes and edges being added to the graph. When the script completes, press ENTER to close the console window.

## Browse using the Data Explorer

You can now go back to Data Explorer in the Azure portal and browse and query your new graph data.

1. In Data Explorer, the new database appears in the Graphs pane. Expand the database and container nodes, and then click **Graph**.

2. Click the **Apply Filter** button to use the default query to view all the vertices in the graph. The data generated by the sample app is displayed in the Graphs pane.

    You can zoom in and out of the graph, you can expand the graph display space, add additional vertices, and move vertices on the display surface.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="View the graph in Data Explorer in the Azure portal":::

## Review SLAs in the Azure portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## Clean up resources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## Next steps

In this quickstart, you've learned how to create an Azure Cosmos DB account, create a graph using the Data Explorer, and run an app. You can now build more complex queries and implement powerful graph traversal logic using Gremlin. 

> [!div class="nextstepaction"]
> [Query using Gremlin](tutorial-query-graph.md)

