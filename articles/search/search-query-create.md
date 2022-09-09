---
title: Create a query
titleSuffix: Azure Cognitive Search
description: Learn how to construct a query request in Cognitive Search, which tools and APIs to use for testing and code, and how query decisions start with index design.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
---

# Creating queries in Azure Cognitive Search

If you are building a query for the first time, this article describes approaches and methods for setting up queries. It also introduces a query request, and explains how field attributes and linguistic analyzers can impact query outcomes.

## What's a query request?

A query is a read-only request against the docs collection of a single search index. It specifies a 'search' parameter contains the query expression, consisting of terms, quote-enclosed phrases, and operators.

Additional parameters provide more definition to the query and response. For example, 'searchFields' scopes query execution to specific fields, 'select' specifies which fields are returned in results, and 'count' returns the number of matches found in the index.

The following example gives you a general idea of a query request by showing a subset of the available parameters. For more information about query composition, see [Query types and compositions](search-query-overview.md) and [Search Documents (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## Choose a client

You'll need a tool like Azure portal or Postman, or code that instantiates a query client using APIs. We recommend the Azure portal or REST APIs for early development and proof-of-concept testing.

### Permissions

Any operation, including query requests, will work under an [admin API key](search-security-api-keys.md), but query requests can optionally use a [query API key](search-security-api-keys.md#create-query-keys). Query API keys are strongly recommended. You can create up to 50 per service and assign different keys to different applications.

In Azure portal, access to tools, wizards, and objects require membership in the Contributor role or above on the service. 

### Use Azure portal to query an index

[Search explorer (portal)](search-explorer.md) is a query interface in the Azure portal that runs queries against indexes on the underlying search service. Internally, the portal makes [Search Documents](/rest/api/searchservice/search-documents) requests, but cannot invoke Autocomplete, Suggestions, or Document Lookup. 

You can select any index and REST API version, including preview. A query string can use simple or full syntax, with support for all query parameters (filter, select, searchFields, and so on). In the portal, when you open an index, you can work with Search Explorer alongside the index JSON definition in side-by-side tabs for easy access to field attributes. Check what fields are searchable, sortable, filterable, and facetable while testing queries.

### Use a REST client

Both Postman and Visual Studio Code (with an extension for Azure Cognitive Search) can function as a query client. Using either tool, you can connect to your search service and send [Search Documents (REST)](/rest/api/searchservice/search-documents) requests. Numerous tutorials and examples demonstrate REST clients for querying indexing. 

Start with either of these articles to learn about each client (both include instructions for queries):

+ [Create a search index using REST and Postman](search-get-started-rest.md)
+ [Get started with Visual Studio Code and Azure Cognitive Search](search-get-started-vs-code.md)

Each request is standalone, so you must provide the endpoint, index name, and API version on every request. Other properties, Content-Type and API key, are passed on the request header. For more information, see [Search Documents (REST)](/rest/api/searchservice/search-documents) for help with formulating query requests.

### Use an SDK

For Cognitive Search, the Azure SDKs implement generally available features. As such, you can use any of the SDKs to query an index. All of them provide a **SearchClient** that has methods to interacting with an index, from loading an index with search documents, to formulating query requests.

| Azure SDK | Client | Examples |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | Pending. |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## Choose a query type: simple | full

If your query is full text search, a query parser will be used to process any text that's passed as search terms and phrases.Azure Cognitive Search offers two query parsers. 

+ The simple parser understands the [simple query syntax](query-simple-syntax.md). This parser was selected as the default for its speed and effectiveness in free form text queries. The syntax supports common search operators (AND, OR, NOT) for term and phrase searches, and prefix (`*`) search (as in "sea*" for Seattle and Seaside). A general recommendation is to try the simple parser first, and then move on to full parser if application requirements call for more powerful queries.

+ The [full Lucene query syntax](query-Lucene-syntax.md#bkmk_syntax), enabled when you add `queryType=full` to the request, is based on the [Apache Lucene Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Full syntax and simple syntax overlap to the extent that both support the same prefix and boolean operations, but the full syntax provides more operators. In full, there are more operators for boolean expressions, and more operators for advanced queries such as fuzzy search, wildcard search, proximity search, and regular expressions.

## Choose query methods

Search is fundamentally a user-driven exercise, where terms or phrases are collected from a search box, or from click events on a page. The following table summarizes the mechanisms by which you can collect user input, along with the expected search experience.

| Input | Experience |
|-------|---------|
| [Search method](/rest/api/searchservice/search-documents) | A user types terms or phrases into a search box, with or without operators, and clicks Search to send the request. Search can be used with filters on the same request, but not with autocomplete or suggestions. |
| [Autocomplete method](/rest/api/searchservice/autocomplete) | A user types a few characters, and queries are initiated after each new character is typed. The response is a completed string from the index. If the string provided is valid, the user clicks Search to send that query to the service. |
| [Suggestions method](/rest/api/searchservice/suggestions) | As with autocomplete, a user types a few characters and incremental queries are generated. The response is a dropdown list of matching documents, typically represented by a few unique or descriptive fields. If any of the selections are valid, the user clicks one and the matching document is returned. |
| [Faceted navigation](/rest/api/searchservice/search-documents#query-parameters) | A page shows clickable navigation links or breadcrumbs that narrow the scope of the search. A faceted navigation structure is composed dynamically based on an initial query. For example, `search=*` to populate a faceted navigation tree composed of every possible category. A faceted navigation structure is created from a query response, but it's also a mechanism for expressing the next query. n REST API reference, `facets` is documented as a query parameter of a Search Documents operation, but it can be used without the `search` parameter.|
| [Filter method](/rest/api/searchservice/search-documents#query-parameters) | Filters are used with facets to narrow results. You can also implement a filter behind the page, for example to initialize the page with language-specific fields. In REST API reference, `$filter` is documented as a query parameter of a Search Documents operation, but it can be used without the `search` parameter.|

## Know your field attributes

If you previously reviewed [query types and composition](search-query-overview.md), you might remember that the parameters on the query request depend on how fields are attributed in an index. For example, to be used in a query, filter, or sort order, a field must be *searchable*, *filterable*, and *sortable*. Similarly, only fields marked as *retrievable* can appear in results. As you begin to specify the `search`, `filter`, and `orderby` parameters in your request, be sure to check attributes as you go to avoid unexpected results.

In the portal screenshot below of the [hotels sample index](search-get-started-portal.md), only the last two fields "LastRenovationDate" and "Rating" can be used in an `"$orderby"` only clause.

![Index definition for the hotel sample](./media/search-query-overview/hotel-sample-index-definition.png "Index definition for the hotel sample")

For a description of field attributes, see [Create Index (REST API)](/rest/api/searchservice/create-index).

## Know your tokens

During indexing, the search engine uses an analyzer to perform text analysis on strings, maximizing the potential for matching at query time. At a minimum, strings are lower-cased, but might also undergo lemmatization and stop word removal. Larger strings or compound words are typically broken up by whitespace, hyphens, or dashes, and indexed as separate tokens. 

The point to take away here is that what you think your index contains, and what's actually in it, can be different. If queries do not return expected results, you can inspect the tokens created by the analyzer through the [Analyze Text (REST API)](/rest/api/searchservice/test-analyzer). For more information about tokenization and the impact on queries, see [Partial term search and patterns with special characters](search-query-partial-matching.md).

## Next steps

Now that you have a better understanding of how query requests work, try the following quickstarts for hands-on experience.

+ [Search explorer](search-explorer.md)
+ [How to query in REST](search-get-started-rest.md)
+ [How to query in .NET](search-get-started-dotnet.md)
+ [How to query in Python](search-get-started-python.md)
+ [How to query in JavaScript](search-get-started-javascript.md)