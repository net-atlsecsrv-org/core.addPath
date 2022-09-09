---
title: Query types 
titleSuffix: Azure Cognitive Search
description: Learn about the types of queries supported in Cognitive Search, including free text, filter, autocomplete and suggestions, geo-search, system queries, and document lookup.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
---
# Querying in Azure Cognitive Search

Azure Cognitive Search offers a rich query language to support a broad range of scenarios, from free text search, to highly-specified query patterns. This article describes query requests, and what kinds of queries you can create.

In Cognitive Search, a query is a full specification of a round-trip **`search`** operation, with parameters that both inform query execution and shape the response coming back. Parameters and parsers determine the type of query request. The following query example is a free text query with a boolean operator, using the [Search Documents (REST API)](/rest/api/searchservice/search-documents), targeting the [hotels-sample-index](search-get-started-portal.md) documents collection.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Parameters used during query execution include:

+ **`queryType`** sets the parser, which is either the [default simple query parser](search-query-simple-examples.md) (optimal for full text search), or the [full Lucene query parser](search-query-lucene-examples.md) used for advanced query constructs like regular expressions, proximity search, fuzzy and wildcard search, to name a few.

+ **`search`** provides the match criteria, usually whole terms or phrases, with or without operators. Any field that is attributed as *searchable* in the index schema is a candidate for this parameter.

+ **`searchFields`** constrains query execution to specific searchable fields.

Parameters used to shape the response:

+ **`select`** specifies which fields to return in the response. Only fields marked as *retrievable* in the index can be used in a select statement.

+ **`top`** returns the specified number of best-matching documents. In this example, only 10 hits are returned. You can use top and skip (not shown) to page the results.

+ **`count`** tells you how many documents in the entire index match overall, which can be more than what are returned. 

+ **`orderby`** is used if you want to sort results by a value, such as a rating or location. Otherwise, the default is to use the relevance score to rank results. A  field must be attributed as *sortable* to be a candidate for this parameter.

The above list is representative but not exhaustive. For the full list of parameters on a query request, see [Search Documents (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## Types of queries

With a few notable exceptions, a query request iterates over inverted indexes that are structured for fast scans, where a match can be found in potentially any field, within any number of search documents. In Cognitive Search, the primary methodology for finding matches is either full text search or filters, but you can also implement other well-known search experiences like autocomplete, or geo-location search. The rest of this article summarizes queries in Cognitive Search and provides links to more information and examples.

## Full text search

If your search app includes a search box that collects term inputs, then full text search is probably the query operation backing that experience. Full text search accepts terms or phrases passed in a **`search`** parameter in all *searchable* fields in your index. Optional boolean operators in the query string can specify inclusion or exclusion criteria. Both the simple parser and full parser support full text search.

In Cognitive Search, full text search is built on the Apache Lucene query engine. Query strings in full text search undergo lexical analysis to make scans more efficient. Analysis includes lower-casing all terms, removing stop words like "the", and reducing terms to primitive root forms. The default analyzer is Standard Lucene.

When matching terms are found, the query engine reconstitutes a search document containing the match using the document key or ID to assemble field values, ranks the documents in order of relevance, and returns the top 50 (by default) in the response or a different number if you specified **`top`**.

If you're implementing full text search, understanding how your content is tokenized will help you debug any query anomalies. Queries over hyphenated strings or special characters could necessitate using an analyzer other than the default standard Lucene to ensure the index contains the right tokens. You can override the default with [language analyzers](index-add-language-analyzers.md#language-analyzer-list) or [specialized analyzers](index-add-custom-analyzers.md#AnalyzerTable) that modify lexical analysis. One example is [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) that treats the entire contents of a field as a single token. This is useful for data like zip codes, IDs, and some product names. For more information, see [Partial term search and patterns with special characters](search-query-partial-matching.md).

If you anticipate heavy use of Boolean operators, which is more likely in indexes that contain large text blocks (a content field or long descriptions), be sure to test queries with the **`searchMode=Any|All`** parameter to evaluate the impact of that setting on boolean search.

## Autocomplete and suggested queries

[Autocomplete or suggested results](search-autocomplete-tutorial.md) are alternatives to **`search`** that fire successive query requests based on partial string inputs (after each character) in a search-as-you-type experience. You can use **`autocomplete`** and **`suggestions`** parameter together or separately, as described in [this tutorial](tutorial-csharp-type-ahead-and-suggestions.md), but you cannot use them with **`search`**. Both completed terms and suggested queries are derived from index contents. The engine will never return a string or suggestion that is non-existent in your index. For more information, see [Autocomplete (REST API)](/rest/api/searchservice/autocomplete) and [Suggestions (REST API)](/rest/api/searchservice/suggestions).

## Filter search

Filters are widely used in apps that include Cognitive Search. On application pages, filters are often visualized as facets in link navigation structures for user-directed filtering. Filters are also used internally to expose slices of indexed content. For example, you might initialize a search page using a filter on a product category, or a language if an index contains fields in both English and French.

You might also need filters to invoke a specialized query form, as described in the following table. You can use a filter with an unspecified search (**`search=*`**) or with a query string that includes terms, phrases, operators, and patterns.

| Filter scenario | Description |
|-----------------|-------------|
| Range filters | In Azure Cognitive Search, range queries are built using the filter parameter. For more information and examples, see [Range filter example](search-query-simple-examples.md#example-4-range-filters). |
| Geo-location search | If a searchable field is of [Edm.GeographyPoint type](/rest/api/searchservice/supported-data-types), you can create a filter expression for "find near me" or map-based search controls. Fields that drive geo-search contain coordinates. For more information and an example, see [Geo-search example](search-query-simple-examples.md#example-5-geo-search). |
| Faceted navigation | A facet navigation structure becomes instrumental in user-directed navigation when you invoke a filter in response to an `onclick` event on a facet. As such, facets and filters go hand-in-hand. If you add facet navigation, you will need filters to complete the experience. For more information, see [How to build a facet filter](search-filters-facets.md). |

> [!NOTE]
> Text that's used in a filter expression is not analyzed during query processing. The text input is presumed to be a verbatim case-sensitive character pattern that either succeeds or fails on the match. Filter expressions are constructed using [OData syntax](query-odata-filter-orderby-syntax.md) and passed in a **`filter`** parameter in all *filterable* fields in your index. For more information, see [Filters in Azure Cognitive Search](search-filters.md).

## Document look-up

In contrast with the previously described query forms, this one retrieves a single [search document by ID](/rest/api/searchservice/lookup-document), with no corresponding index search or scan. Only the one document is requested and returned. When a user selects an item in search results, retrieving the document and populating a details page with fields is a typical response, and a document look-up is the operation that supports it.

## Advanced search: fuzzy, wildcard, proximity, regex

An advanced query form depends on the Full Lucene parser and operators that trigger a specific query behavior.

| Query type | Usage | Examples and more information |
|------------|--------|------------------------------|
| [Fielded search](query-lucene-syntax.md#bkmk_fields) | **`search`**  parameter, **`queryType=full`**  | Build a composite query expression targeting a single field. <br/>[Fielded search example](search-query-lucene-examples.md#example-2-fielded-search) |
| [fuzzy search](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** parameter, **`queryType=full`** | Matches on terms having a similar construction or spelling. <br/>[Fuzzy search example](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [proximity search](query-lucene-syntax.md#bkmk_proximity) | **`search`** parameter, **`queryType=full`** | Finds terms that are near each other in a document. <br/>[Proximity search example](search-query-lucene-examples.md#example-4-proximity-search) |
| [term boosting](query-lucene-syntax.md#bkmk_termboost) | **`search`** parameter, **`queryType=full`** | Ranks a document higher if it contains the boosted term, relative to others that don't. <br/>[Term boosting example](search-query-lucene-examples.md#example-5-term-boosting) |
| [regular expression search](query-lucene-syntax.md#bkmk_regex) | **`search`** parameter, **`queryType=full`** | Matches based on the contents of a regular expression. <br/>[Regular expression example](search-query-lucene-examples.md#example-6-regex) |
|  [wildcard or prefix search](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parameter with ***`~`** or **`?`**, **`queryType=full`**| Matches based on a prefix and tilde (`~`) or single character (`?`). <br/>[Wildcard search example](search-query-lucene-examples.md#example-7-wildcard-search) |

## Next steps

For a closer look at query implementation, review the examples for each syntax. If you are new to full text search, a closer look at what the query engine does might be an equally good choice.

+ [Simple query examples](search-query-simple-examples.md)
+ [Lucene syntax query examples for building advanced queries](search-query-lucene-examples.md)
+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)