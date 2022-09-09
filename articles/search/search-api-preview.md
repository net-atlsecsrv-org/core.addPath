---
title: REST API version 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API Version 2019-05-06-Preview includes experimental features such as knowledge store and customer-managed encryption keys.

manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
---
# Azure Cognitive Search service REST api-version 2019-05-06-Preview

This article describes the `api-version=2019-05-06-Preview` version of Search service REST API, offering experimental features not yet generally available.

> [!NOTE]
> Preview features are available for testing and experimentation with the goal of gathering feedback and are subject to change. We strongly advise against using preview APIs in production applications.


## New in 2019-05-06-Preview

+ [Incremental indexing](cognitive-search-incremental-indexing-conceptual.md) is a new mode for indexing that adds state and caching to a skillset, allowing you to reuse existing output when source data, indexer, and skillset definitions are unchanged. This feature applies only to enrichments defined a cognitive skillset.

+ [Cosmos DB indexer](search-howto-index-cosmosdb.md) supports MongoDB API, Gremlin API, and Cassandra API.

+ [Azure Data Lake Storage Gen2 indexer](search-howto-index-azure-data-lake-storage.md) can index content and metadata from Data Lake Storage Gen2.

+ [Document Extraction (preview)](cognitive-search-skill-document-extraction.md) is a cognitive skill used during indexing that allows you to extract the contents of a file from within a skillset. Previously, document cracking only occurred prior to skillset execution. With the addition of this skill, you can also perform this operation within skillset execution.

+ [Text Translation (preview)](cognitive-search-skill-text-translation.md) is a cognitive skill used during indexing that evaluates text and, for each record, returns the text translated to the specified target language.

+ [Knowledge store](knowledge-store-concept-intro.md) is a new destination of an AI-based enrichment pipeline. The physical data structure exists in Azure Blob storage and Azure Table storage, and it is created and populated when you run an indexer that has an attached cognitive skillset. The definition of a knowledge store itself is specified within a skillset definition. Within the knowledge store definition, you control the physical structures of your data through *projection* elements that determine how data is shaped, whether data is stored in Table storage or Blob storage, and whether there are multiple views.

+ [Customer-managed encryption keys](search-security-manage-encryption-keys.md) for service-side encryption-at-rest is also a new preview feature. In addition to the built-in encryption-at-rest managed by Microsoft, you can apply an additional layer of encryption where you are the sole owner of the keys.

## Earlier preview features

Features announced in earlier previews are still in public preview. If you're calling an API with an earlier preview api-version, you can continue to use that version or switch to `2019-05-06-Preview` with no changes to expected behavior.

+ [moreLikeThis query parameter](search-more-like-this.md) finds documents that are relevant to a specific document. This feature has been in earlier previews. 

+ [CSV blob indexing](search-howto-index-csv-blobs.md) creates one document per line, as opposed to one document per text blob.

## How to call a preview API

Older previews are still operational but become stale over time. If your code calls `api-version=2016-09-01-Preview` or `api-version=2017-11-11-Preview`, those calls are still valid. However, only the newest preview version is refreshed with improvements. 

The following example syntax illustrates a call to the preview API version.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search service is available in multiple versions. For more information, see [API versions](search-api-versions.md).

## Next steps

Review the Search REST API reference documentation. If you encounter problems, ask us for help on [StackOverflow](https://stackoverflow.com/) or [contact support](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Search service REST API Reference](https://docs.microsoft.com/rest/api/searchservice/)