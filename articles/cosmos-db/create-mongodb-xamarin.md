---
title: Build a Xamarin app with .NET and Azure Cosmos DB's API for MongoDB
description: Presents a Xamarin code sample you can use to connect to and query with Azure Cosmos DB's API for MongoDB
author: codemillmatt 
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: masoucou
ms.custom: devx-track-csharp

---

# QuickStart: Build a Xamarin.Forms app with .NET SDK and Azure Cosmos DB's API for MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB is Microsoft's globally distributed multi-model database service. You can quickly create and query document, key/value, and graph databases, all of which benefit from the global distribution and horizontal scale capabilities at the core of Azure Cosmos DB.

This quickstart demonstrates how to create a [Cosmos account configured with Azure Cosmos DB's API for MongoDB](mongodb-introduction.md), document database, and collection using the Azure portal. You'll then build a todo app Xamarin.Forms app by using the [MongoDB .NET driver](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## Prerequisites to run the sample app

To run the sample, you'll need [Visual Studio](https://www.visualstudio.com/downloads/) or [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and a valid Azure CosmosDB account.

If you don't already have Visual Studio, download [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) with the **Mobile development with .NET** workload installed with setup.

If you prefer to work on a Mac, download [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and run the setup.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## Create a database account

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

The sample described in this article is compatible with MongoDB.Driver version 2.6.1.

## Clone the sample app

First, download the sample app from GitHub. It implements a todo app with MongoDB's document storage model.



# [Windows](#tab/windows)

1. On Windows open a command prompt or on Mac open the terminal, create a new folder named git-samples, then close the window.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Open a git terminal window, such as git bash, and use the `cd` command to change to the new folder to install the sample app.

    ```bash
    cd "C:\git-samples"
    ```

3. Run the following command to clone the sample repository. This command creates a copy of the sample app on your computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

If you don't wish to use git, you can also [download the project as a ZIP file](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## Review the code

This step is optional. If you're interested in learning how the database resources are created in the code, you can review the following snippets. Otherwise, you can skip ahead to [Update your connection string](#update-your-connection-string).

The following snippets are all taken from the `MongoService` class, found at the following path: src/TaskList.Core/Services/MongoService.cs.

* Initialize the Mongo Client.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(new MongoUrl(APIKeys.ConnectionString));

    settings.SslSettings = new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Retrieve a reference to the database and collection. The MongoDB .NET SDK will automatically create both the database and collection if they do not already exist.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings 
    {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Retrieve all documents as a List.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Query for particular documents.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Create a task and insert it into the collection.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Update a task in a collection.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Delete a task from a collection.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## Update your connection string

Now go back to the Azure portal to get your connection string information and copy it into the app.

1. In the [Azure portal](https://portal.azure.com/), in your Azure Cosmos DB account, in the left navigation click **Connection String**, and then click **Read-write Keys**. You'll use the copy buttons on the right side of the screen to copy the Primary Connection String in the next steps.

2. Open the **APIKeys.cs** file in the **Helpers** directory of the **TaskList.Core** project.

3. Copy your **primary connection string** value from the portal (using the copy button) and make it the value of the **ConnectionString** field in your **APIKeys.cs** file.

4. Remove `&replicaSet=globaldb` from the connection string. You will get a runtime error if you do not remove that value from the query string.

> [!IMPORTANT]
> You must remove the `&replicaSet=globaldb` key/value pair from the connection string's query string in order to avoid a runtime error.

You've now updated your app with all the info it needs to communicate with Azure Cosmos DB.

## Run the app

### Visual Studio 2019

1. In Visual Studio, right-click on each project in **Solution Explorer** and then click **Manage NuGet Packages**.
2. Click **Restore all NuGet packages**.
3. Right click on the **TaskList.Android** and select **Set as startup project**.
4. Press F5 to start debugging the application.
5. If you want to run on iOS, first your machine is connected to a Mac (here are [instructions](/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) on how to do so).
6. Right click on **TaskList.iOS** project and select **Set as startup project**.
7. Click F5 to start debugging the application.

### Visual Studio for Mac

1. In the platform dropdown list, select either TaskList.iOS or TaskList.Android, depending which platform you want to run on.
2. Press cmd+Enter to start debugging the application.

## Review SLAs in the Azure portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## Clean up resources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## Next steps

In this quickstart, you've learned how to create an Azure Cosmos DB account and run a Xamarin.Forms app using the API for MongoDB. You can now import additional data to your Cosmos DB account.

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB configured with Azure Cosmos DB's API for MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)