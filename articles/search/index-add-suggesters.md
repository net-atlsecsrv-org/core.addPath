---
title: Create a suggester
titleSuffix: Azure Cognitive Search
description: Enable type-ahead query actions in Azure Cognitive Search by creating suggesters and formulating requests that invoke autocomplete or autosuggested query terms.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
---

# Create a suggester to enable autocomplete and suggested results in a query

In Azure Cognitive Search, "search-as-you-type" is enabled through a **suggester** construct added to a [search index](search-what-is-an-index.md). A suggester supports two experiences: *autocomplete*, which completes the term or phrase, and *suggestions* that return a short list of matching documents.  

The following screenshot from [Create your first app in C#](tutorial-csharp-type-ahead-and-suggestions.md) illustrates both. Autocomplete anticipates a potential term, finishing "tw" with "in". Suggestions are mini search results, where a field like hotel name represents a matching hotel search document from the index. For suggestions, you can surface any field that provides descriptive information.

![Visual comparison of autocomplete and suggested queries](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visual comparison of autocomplete and suggested queries")

You can use these features separately or together. To implement these behaviors in Azure Cognitive Search, there is an index and query component. 

+ In the index, add a suggester to an index. You can use the portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index), or [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). The remainder of this article is focused on creating a suggester.

+ In the query request, call one of the [APIs listed below](#how-to-use-a-suggester).

Search-as-you-type support is enabled on a per-field basis for string fields. You can implement both typeahead behaviors within the same search solution if you want an experience similar to the one indicated in the screenshot. Both requests target the *documents* collection of specific index and responses are returned after a user has provided at least a three character input string.

## What is a suggester?

A suggester is a data structure that supports search-as-you-type behaviors by storing prefixes for matching on partial queries. Similar to tokenized terms, prefixes are stored in inverted indexes, one for each field specified in a suggester fields collection.

When creating prefixes, a suggester has its own analysis chain, similar to the one used for full text search. However, unlike analysis in full text search, a suggester can only operate over fields that use the standard Lucene analyzer (default) or a [language analyzer](index-add-language-analyzers.md). Fields that use [custom analyzers](index-add-custom-analyzers.md) or [predefined analyzers](index-add-custom-analyzers.md#predefined-analyzers-reference) (with the exception of standard Lucene) are explicitly disallowed to prevent poor outcomes.

> [!NOTE]
> If you need to work around the analyzer constraint, use two separate fields for the same content. This will allow one of the fields to have a suggester, while the other can be set up with a custom analyzer configuration.

## Define a suggester

Although a suggester has several properties, it is primarily a collection of fields for which you are enabling a search-as-you-type experience. For example, a travel app might want to enable autocomplete on destinations, cities, and attractions. As such, all three fields would go in the fields collection.

To create a suggester, add one to an index schema. You can have one suggester in an index (specifically, one suggester in the suggesters collection). A suggester takes a list of fields. 

+ For suggestions, choose fields that best represent a single result. Names, titles, or other unique fields that distinguish among documents work best. If fields consist of similar or identical values, the suggestions will be composed of identical results and a user won't know which one to click.

+ Make sure each field in the suggester `sourceFields` list uses either the default standard Lucene analyzer (`"analyzer": null`) or a [language analyzer](index-add-language-analyzers.md) (for example, `"analyzer": "en.Microsoft"`). 

  Your choice of an analyzer determines how fields are tokenized and subsequently prefixed. For example, for a hyphenated string like "context-sensitive", using a language analyzer will result in these token combinations: "context", "sensitive", "context-sensitive". Had you used the standard Lucene analyzer, the hyphenated string would not exist.

> [!TIP]
> Consider using the [Analyze Text API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) for insight into how terms are tokenized and subsequently prefixed. Once you build an index, you can try various analyzers on a string to view the tokens it emits.

### When to create a suggester

The best time to create a suggester is when you are also creating the field definition itself.

If you try to create a suggester using pre-existing fields, the API will disallow it. Prefixes are generated during indexing, when partial terms in two or more character combinations are tokenized alongside whole terms. Given that existing fields are already tokenized, you will have to rebuild the index if you want to add them to a suggester. For more information, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

### Create using the REST API

In the REST API, add suggesters through [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) or 
[Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

### Create using the .NET SDK

In C#, define a [Suggester object](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` is a collection but it can only take one item. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### Property reference

|Property      |Description      |
|--------------|-----------------|
|`name`        |The name of the suggester.|
|`searchMode`  |The strategy used to search for candidate phrases. The only mode currently supported is `analyzingInfixMatching`, which performs flexible matching of phrases at the beginning or in the middle of sentences.|
|`sourceFields`|A list of one or more fields that are the source of the content for suggestions. Fields must be of type `Edm.String` and `Collection(Edm.String)`. If an analyzer is specified on the field, it must be a named analyzer from [this list](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (not a custom analyzer).<p/> As a best practice, specify only those fields that lend themselves to an expected and appropriate response, whether it's a completed string in a search bar or a dropdown list.<p/>A hotel name is a good candidate because it has precision. Verbose fields like descriptions and comments are too dense. Similarly, repetitive fields, such as categories and tags, are less effective. In the examples, we include "category" anyway to demonstrate that you can include multiple fields. |

<a name="how-to-use-a-suggester"></a>

## Use a suggester

A suggester is used in a query. After a suggester is created, call the appropriate API in your query logic to invoke the feature. 

+ [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync method](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync method](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API usage is illustrated in the following call to the Autocomplete REST API. There are two takeaways from this example. First, as with all queries, the operation is against the documents collection of an index. Second, you can add query parameters. While many query parameters are common to both APIs, the list is different for each one.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

If a suggester is not defined in the index, a call to autocomplete or suggestions will fail.

## Sample code

+ [Create your first app in C#](tutorial-csharp-type-ahead-and-suggestions.md) sample demonstrates a suggester construction, suggested queries, autocomplete, and faceted navigation. This code sample runs on a sandbox Azure Cognitive Search service and uses a pre-loaded Hotels index so all you have to do is press F5 to run the application. No subscription or sign-in is necessary.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) is an older sample containing both C# and Java code. It also demonstrates a suggester construction, suggested queries, autocomplete, and faceted navigation. This code sample uses the hosted [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) sample data. 

## Next steps

We recommend the following example to see how the requests are formulated.

> [!div class="nextstepaction"]
> [Suggestions and autocomplete examples](search-autocomplete-tutorial.md) 
