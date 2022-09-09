---
title: include file
description: include file
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
---

The article uses a Visual Studio code extension (preview) for Azure Cognitive Search REST APIs.

> [!IMPORTANT] 
> This feature is currently in public preview. Preview functionality is provided without a service level agreement, and is not recommended for production workloads. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## Prerequisites

The following services and tools are required for this quickstart. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Azure Cognitive Search for Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Create an Azure Cognitive Search service](../search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

## Copy a key and URL

REST calls require the service URL and an access key on every request. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. An example endpoint might look like `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](../media/search-get-started-rest/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Having a valid key establishes trust, on a per request basis, between the application sending the request and the service that handles it.

## Install the extension

Start by opening [VS Code](https://code.visualstudio.com). Select the **Extensions** tab on the activity bar then search for *Azure Cognitive Search*. Find the extension in the search results, and select **Install**.

![VS Code extension pane](../media/search-get-started-rest/download-extension.png "Downloading the VS Code extension")

Alternatively, you can install  the [Azure Cognitive Search extension](https://aka.ms/vscode-search) from the VS Code marketplace in a web browser.

You should see a new Azure tab appear on the activity bar if you didn't already have it.

![VS Code Azure pane](../media/search-get-started-rest/azure-pane.png "Azure pane in VS Code")

## Connect to your subscription

Select **Sign in to Azure...** and log into your Azure Account.

You should see your subscriptions appear. Select the subscription to see a list of the search services in the subscription.

![VS Code Azure subscriptions](../media/search-get-started-rest/subscriptions.png "Subscriptions in VS Code")

To limit the subscriptions displayed, open the command palette (Ctrl+Shift+P or Cmd+Shift+P) and search for *Azure* or *Select Subscriptions*. There are also commands available for signing in and out of your Azure account.

When you expand the search service, you will see tree items for each of the Cognitive Search resources: indexes, data sources, indexers, skillsets, and synonym maps.

![VS Code Azure search tree](../media/search-get-started-rest/search-tree.png "VS Code Azure search tree")

These tree items can be expanded to show any resources you have in your search service

## 1 - Create an index

To get started with Azure Cognitive Search, you first need to create a search index. This is done using the [Create Index REST API](/rest/api/searchservice/create-index). 

With the VS Code extension, you only need to worry about the body of the request. For this quickstart, we provide a sample index definition and corresponding documents.

### Index definition

The index definition below is a sample schema for fictitious hotels.

The `fields` collection defines the structure of documents in the search index. Each field has a data type and a number of additional attributes that determine how the field can be used.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

To create a new index, right-click on **Indexes** and then select **Create new index**. An editor with a name similar to `indexes-new-28c972f661.azsindex` will pop up. 

Paste the index definition from above into the window. Save the file and select **Upload** when prompted if you want to update the index. This will create the index and it will be available in the tree view.

![Gif of creating an index](../media/search-get-started-rest/create-index.gif)

If there is a problem with your index definition, you should see an error message pop up explaining the error.

![Create index error message](../media/search-get-started-rest/create-index-error.png)

If this happens, fix the issue and resave the file.

## 2 - Load documents

Creating the index and populating the index are separate steps. In Azure Cognitive Search, the index contains all searchable data. In this scenario, the data is provided as JSON documents. The [Add, Update, or Delete Documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) is used for this task. 

To add new documents in VS Code:

1. Expand the `hotels-quickstart` index you created. Right-click on **Documents** and select **Create new document**.

    ![Create a document](../media/search-get-started-rest/create-document.png)

2. This will open up a JSON editor that has inferred the schema of your index.

    ![Create a document json](../media/search-get-started-rest/create-document-2.png)

3. Paste in the JSON below and then save the file. A prompt will open up asking you to confirm your changes. Select **Upload** to save the changes.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Repeat this process for the three remaining documents

    Document 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Document 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Document 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
        }
    }
    ```

At this point, you should see all four documents available in the documents section.

![status after uploading all documents](../media/search-get-started-rest/create-document-finish.png)

## 3 - Search an index

Now that the index and document set are loaded, you can issue queries against them using [Search Documents REST API](/rest/api/searchservice/search-documents).

To do this in VS Code:

1. Right-click the index you want to search and select **Search index**. This will open an editor with a name similar to `sandbox-b946dcda48.azs`.

    ![search view of extension](../media/search-get-started-rest/search-vscode.png)

2. A simple query is autopopulated. Press **Ctrl+Alt+R** or **Cmd+Alt+R** to submit the query. You'll see the results pop up in a window to the left.

    ![search results in extension](../media/search-get-started-rest/search-results.png)


### Example queries

Try a few other query examples to get a feel for the syntax. There's four additional queries below for you to try. You can add multiple queries to the same editor. When you press **Ctrl+Alt+R** or **Cmd+Alt+R**, the line your cursor determines which query will be submitted.

![queries and results side-by-side](../media/search-get-started-rest/all-searches.png)

In the first query, we search `boutique` and `select` only certain fields. It's a best practice to only `select` the fields you need because pulling back unnecessary data can add latency to your queries. The query also sets `$count=true` to return the total number of results with the search results.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

In the next query, we specify the search term `wifi` and also include a filter to only return results where the state is equal to `'FL'`. Results are also ordered by the Hotel's `Rating`.

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Next, the search is limited to a single searchable field using the `searchFields` parameter. This is a great option to make your query more efficient if you know you're only interested in matches in certain fields.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Another common option to include in a query is `facets`. Facets allow you to build out filters on your UI to make it easy for users to know what values they can filter down to.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## Open index in the portal

If you'd like to view your search service in the portal, right-click the name of the search service and select **Open in Portal**. This will take you to the search service in the Azure portal.