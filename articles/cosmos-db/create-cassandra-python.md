---
title: 'Quickstart: Cassandra API with Python - Azure Cosmos DB'
description: This quickstart shows how to use the Azure Cosmos DB's Apache Cassandra API to create a profile application with Python.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python

---
# Quickstart: Build a Cassandra app with Python SDK and Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In this quickstart, you create an Azure Cosmos DB Cassandra API account, and use a Cassandra Python app cloned from GitHub to create a Cassandra database and container. Azure Cosmos DB is a multi-model database service that lets you quickly create and query document, table, key-value, and graph databases with global distribution and horizontal scale capabilities.

## Prerequisites

- An Azure account with an active subscription. [Create one for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Or [try Azure Cosmos DB for free](https://azure.microsoft.com/try/cosmosdb/) without an Azure subscription.
- [Python 2.7.14+ or 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Python Driver for Apache Cassandra](https://github.com/datastax/python-driver).

## Create a database account

Before you can create a document database, you need to create a Cassandra account with Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## Clone the sample application

Now let's clone a Cassandra API app from GitHub, set the connection string, and run it. You see how easy it is to work with data programmatically. 

1. Open a command prompt. Create a new folder named `git-samples`. Then, close the command prompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open a git terminal window, such as git bash, and use the `cd` command to change to the new folder to install the sample app.

    ```bash
    cd "C:\git-samples"
    ```

3. Run the following command to clone the sample repository. This command creates a copy of the sample app on your computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## Review the code

This step is optional. If you're interested to learn how the code creates the database resources, you can review the following snippets. The snippets are all taken from the *pyquickstart.py* file. Otherwise, you can skip ahead to [Update your connection string](#update-your-connection-string). 

* The `cluster` is initialized with `contactPoint` and `port` information that is retrieved from the Azure portal. The `cluster` then connects to the Azure Cosmos DB Cassandra API by using the `connect()` method. An authorized connection is established by using the username, password, and the default certificate or an explicit certificate if you provide one within the config file.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* A new keyspace is created.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* A new table is created.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* Key/value entities are inserted.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* Query to get all key values.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* Query to get a key-value.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## Update your connection string

Now go back to the Azure portal to get your connection string information and copy it into the app. The connection string enables your app to communicate with your hosted database.

1. In your Azure Cosmos DB account in the [Azure portal](https://portal.azure.com/), select **Connection String**. 

1. Use the :::image type="icon" source="./media/create-cassandra-python/copy.png"::: button on the right side of the screen to copy the top value, the CONTACT POINT. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="View and copy an access user name, password and contact point in the Azure portal, connection string blade":::

1. Open the *config.py* file. 

1. Paste the CONTACT POINT value from the portal over `<FILLME>` on line 10.

    Line 10 should now look similar to 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. Copy the USERNAME value from the portal and paste it over `<FILLME>` on line 6.

    Line 6 should now look similar to 

    `'username': 'cosmos-db-quickstart',`
    
1. Copy the PASSWORD value from the portal and paste it over `<FILLME>` on line 8.

    Line 8 should now look similar to

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. Save the *config.py* file.
    
## Use the X509 certificate

1. Download the Baltimore CyberTrust Root certificate locally from [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Rename the file using the file extension *.cer*.

   The certificate has serial number `02:00:00:b9` and SHA1 fingerprint `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Open *pyquickstart.py* and change the `path\to\cert` to point to your new certificate.

3. Save *pyquickstart.py*.

## Run the Python app

1. Use the cd command in the git terminal to change into the `azure-cosmos-db-cassandra-python-getting-started` folder. 

2. Run the following commands to install the required modules:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Run the following command to start your Python application:

    ```
    python pyquickstart.py
    ```

3. Verify the results as expected from the command line.

    Press CTRL+C to stop execution of the program and close the console window. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="View and verify the output":::
    
4. In the Azure portal, open **Data Explorer** to query, modify, and work with this new data. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="View the data in Data Explorer":::

## Review SLAs in the Azure portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## Clean up resources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## Next steps

In this quickstart, you learned how to create an Azure Cosmos DB account with Cassandra API, and run a Cassandra Python app that creates a Cassandra database and container. You can now import additional data into your Azure Cosmos DB account. 

> [!div class="nextstepaction"]
> [Import Cassandra data into Azure Cosmos DB](cassandra-import-data.md)

