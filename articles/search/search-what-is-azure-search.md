---
title: Introduction to Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive  Search is a fully managed cloud search service from Microsoft. Learn about use cases, the development workflow, comparisons to other Microsoft search products, and how to get started.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/24/2020
ms.custom: contperf-fy21q1
---
# What is Azure Cognitive Search?

Azure Cognitive Search ([formerly known as "Azure Search"](whats-new.md#new-service-name)) is a cloud search service that gives developers APIs and tools for building a rich search experience over private, heterogeneous content in web, mobile, and enterprise applications. 

When you create a Cognitive Search service, you get:

+ A search engine that performs indexing and query execution
+ Persistent storage of search indexes that you create and manage
+ A query language for composing simple to complex queries
+ AI-centered analysis, creating searchable content out of images, raw text, application files
+ Integration with Azure data through search indexers, automating data import and refresh

Architecturally, a search service sits in between the external data stores that contain your un-indexed data, and a client app that sends query requests to a search index and handles the response.

![Azure Cognitive Search architecture](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search architecture")

Outwardly, a search service integrates with other Azure services in the form of *indexers* that automate data ingestion/retrieval from Azure data sources, and *skillsets* that incorporate consumable AI from Cognitive Services, such as image and text analysis, or custom AI that you create in Azure Machine Learning or wrap inside Azure Functions.

On the search service itself, the two primary workloads are *indexing* and *querying*. 

+ Indexing brings text into to your search service and makes it searchable. Internally, inbound text is processed into tokens and stored in inverted indexes for fast scans. 

  Within indexing, you have the option of adding *AI enrichment* through [cognitive skills](cognitive-search-working-with-skillsets.md), either predefined ones from Microsoft or custom skills that you create. The subsequent analysis and transformations can result in new information and structures that did not previously exist, providing high utility for many search and knowledge mining scenarios.

+ Once an index is populated with searchable data, your client app sends query requests to a search service and handles responses. All query execution is over a search index that you create, own, and store in your service. In your client app, the search experience is defined using APIs from Azure Cognitive Search, and can include relevance tuning, autocomplete, synonym matching, fuzzy matching, pattern matching, filter, and sort.

Functionality is exposed through a simple [REST API](/rest/api/searchservice/) or [.NET SDK](search-howto-dotnet-sdk.md) that masks the inherent complexity of information retrieval. You can also use the Azure portal for service administration and content management, with tools for prototyping and querying your indexes and skillsets. Because the service runs in the cloud, infrastructure and availability are managed by Microsoft.

## Why use Cognitive Search

Azure Cognitive Search is well-suited for the following application scenarios:

+ Consolidate heterogeneous content into a private, user-defined search index. You can populate a search index with streams of JSON documents from any source. For supported sources on Azure, use an *indexer* to automate indexing. Control over the index schema and refresh schedule is a key reason for using Cognitive Search.

+ Easy implementation of search-related features. Search APIs simplify query construction, faceted navigation, filters (including geo-spatial search), synonym mapping, autocomplete, and relevance tuning. Using built-in features, you can satisfy end-user expectations for a search experience similar to commercial web search engines.

+ Raw content is large undifferentiated text or image files or application files stored in Azure Blob storage or Cosmos DB. You can apply [cognitive skills](cognitive-search-concept-intro.md) during indexing to identify and extract text, create structure, or create new information such as translated text or entities.

+ Content needs linguistic or custom text analysis. If you have non-English content, Azure Cognitive Search supports both Lucene analyzers and Microsoft's natural language processors. You can also configure analyzers to achieve specialized processing of raw content, such as filtering out diacritics, or recognizing and preserving patterns in strings.

For more information about specific functionality, see [Features of Azure Cognitive Search](search-features-list.md)

## How to get started

An end-to-end exploration of core search features can be achieved in four steps:

1. [**Create a search service**](search-create-service-portal.md) at the Free tier shared with other subscribers, or a [paid tier](https://azure.microsoft.com/pricing/details/search/) for dedicated resources used only by your service. All quickstarts and tutorials can be completed on the free service.

1. [**Create a search index**](search-what-is-an-index.md) using the portal, [REST API](/rest/api/searchservice/create-index). [.NET SDK](search-howto-dotnet-sdk.md), or another SDK. The index schema defines the structure of searchable content.

1. [**Upload content**](search-what-is-data-import.md) to the index. Use the ["push" model](tutorial-optimize-indexing-push-api.md) to push JSON documents from any source, or use the ["pull" model (indexers)](search-indexer-overview.md) if your source data is on Azure.

1. [**Query an index**](search-query-overview.md) using [Search explorer](search-explorer.md) in the portal, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search), or another SDK.

> [!TIP]
> Minimize steps by starting with the [**Import data wizard**](search-get-started-portal.md) and an Azure data source to create, load, and query an index in minutes.

## How it compares

Customers often ask how Azure Cognitive Search compares with other search-related solutions. The following table summarizes key differences.

| Compared to | Key differences |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) is for Microsoft 365 authenticated users who need to query over content in SharePoint. It's offered as a ready-to-use search experience, enabled and configured by administrators, with the ability to accept external content through connectors from Microsoft and other sources. If this describes your scenario, then Microsoft Search with Microsoft 365 is an attractive option to explore.<br/><br/>In contrast, Azure Cognitive Search executes queries over an index that you define, populated with data and documents you own, often from diverse sources. Azure Cognitive Search has crawler capabilities for some Azure data sources through [indexers](search-indexer-overview.md), but you can push any JSON document that conforms to your index schema into a single, consolidated searchable resource. You can also customize the indexing pipeline to include machine learning and lexical analyzers. Because Cognitive Search is built to be a plug-in component in larger solutions, you can integrate search into almost any app, on any platform.|
|Bing | [Bing Web Search API](../cognitive-services/bing-web-search/index.yml) searches the indexes on Bing.com for matching terms you submit. Indexes are built from HTML, XML, and other web content on public sites. Built on the same foundation, [Bing Custom Search](/azure/cognitive-services/bing-custom-search/) offers the same crawler technology for web content types, scoped to individual web sites.<br/><br/>In Cognitive Search, you can define and populate the index. You can use [indexers](search-indexer-overview.md) to crawl data on Azure data sources, or push any index-conforming JSON document to your search service. |
|Database search | Many database platforms include a built-in search experience. SQL Server has [full text search](/sql/relational-databases/search/full-text-search). Cosmos DB and similar technologies have queryable indexes. When evaluating products that combine search and storage, it can be challenging to determine which way to go. Many solutions use both: DBMS for storage, and Azure Cognitive Search for specialized search features.<br/><br/>Compared to DBMS search, Azure Cognitive Search stores content from heterogeneous sources and offers specialized text processing features such as linguistic-aware text processing (stemming, lemmatization, word forms) in [56 languages](/rest/api/searchservice/language-support). It also supports autocorrection of misspelled words, [synonyms](/rest/api/searchservice/synonym-map-operations), [suggestions](/rest/api/searchservice/suggestions), [scoring controls](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facets](./search-filters-facets.md),  and [custom tokenization](/rest/api/searchservice/custom-analyzers-in-azure-search). The [full text search engine](search-lucene-query-architecture.md) in Azure Cognitive Search is built on Apache Lucene, an industry standard in information retrieval. However, while Azure Cognitive Search persists data in the form of an inverted index, it is not a replacement for true data storage and we don't recommend using it in that capacity. For more information, see this [forum post](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Resource utilization is another inflection point in this category. Indexing and some query operations are often computationally intensive. Offloading search from the DBMS to a dedicated solution in the cloud preserves system resources for transaction processing. Furthermore, by externalizing search, you can easily adjust scale to match query volume.|
|Dedicated search solution | Assuming you have decided on dedicated search with full spectrum functionality, a final categorical comparison is between on premises solutions or a cloud service. Many search technologies offer controls over indexing and query pipelines, access to richer query and filtering syntax, control over rank and relevance, and features for self-directed and intelligent search. <br/><br/>A cloud service is the right choice if you want a turn-key solution with minimal overhead and maintenance, and adjustable scale. <br/><br/>Within the cloud paradigm, several providers offer comparable baseline features, with full-text search, geo-search, and the ability to handle a certain level of ambiguity in search inputs. Typically, it's a [specialized feature](search-features-list.md), or the ease and overall simplicity of APIs, tools, and management that determines the best fit. |

Among cloud providers, Azure Cognitive Search is strongest for full text search workloads over content stores and databases on Azure, for apps that rely primarily on search for both information retrieval and content navigation. 

Key strengths include:

+ Azure data integration (crawlers) at the indexing layer
+ Azure portal for central management
+ Azure scale, reliability, and world-class availability
+ AI processing of raw data to make it more searchable, including text from images, or finding patterns in unstructured content.
+ Linguistic and custom analysis, with analyzers for solid full text search in 56 languages
+ [Core features common to search-centric apps](search-features-list.md): scoring, faceting, suggestions, synonyms, geo-search, and more.

Among our customers, those able to leverage the widest range of features in Azure Cognitive Search include online catalogs, line-of-business programs, and document discovery applications.

## Watch this video

In this 15-minute video, program manager Luis Cabrera introduces Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]