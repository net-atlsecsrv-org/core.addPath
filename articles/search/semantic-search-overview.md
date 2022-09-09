---
title: Semantic search
titleSuffix: Azure Cognitive Search
description: Learn how Cognitive Search is using deep learning semantic search models from Bing to make search results more intuitive.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2021
ms.custom: references_regions
---
# Semantic search in Azure Cognitive Search

> [!IMPORTANT]
> Semantic search is in public preview, available through the preview REST API and portal. Preview features are offered as-is, under [Supplemental Terms of Use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), and are not guaranteed to have the same implementation at general availability. These features are billable. For more information, see [Availability and pricing](semantic-search-overview.md#availability-and-pricing).

Semantic search is a collection of query-related capabilities that add semantic relevance and language understanding to search results. This article is a high-level introduction to semantic search all-up, with descriptions of each feature and how they work collectively. The embedded video describes the technology, and the section at the end covers availability and pricing.

We recommend reviewing this article for background, but if you'd rather get started right away, follow these steps:

1. [Sign up for the preview](https://aka.ms/SemanticSearchPreviewSignup), assuming a service that meets [regional and tier requirements](#availability-and-pricing).
1. Create new or modify existing queries to return [semantic captions and highlights](semantic-how-to-query-request.md).
1. Add a few more properties to also return [semantic answers](semantic-answers.md).
1. Optionally, include a [spell check](speller-how-to-add.md) query property to maximize precision and recall.

## What is semantic search?

Semantic search is an optional layer of search-related AI that extends the traditional query execution pipeline with a semantic ranking model, and returns additional properties that improve the user experience.

*Semantic ranking* looks for context and relatedness among terms, elevating matches that make more sense given the query. Language understanding finds *captions* and *answers* within your content that summarize the matching document or answer a question, which can then be rendered on a search results page for a more productive search experience.

State-of-the-art pretrained models are used for summarization and ranking. To maintain the fast performance that users expect from search, semantic summarization and ranking are applied to just the top 50 results, as scored by the [default similarity scoring algorithm](index-similarity-and-scoring.md#similarity-ranking-algorithms). Using those results as the document corpus, semantic ranking re-scores those results based on the semantic strength of the match.

The underlying technology is from Bing and Microsoft Research, and integrated into the Cognitive Search infrastructure as an add-on feature. For more information about the research and AI investments backing semantic search, see [How AI from Bing is powering Azure Cognitive Search (Microsoft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

The following video provides an overview of the capabilities.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## Components and workflow

Semantic search improves precision and recall with the addition of the following capabilities:

| Feature | Description |
|---------|-------------|
| [Spell check](speller-how-to-add.md) | Corrects typos before the query terms reach the search engine. |
| [Semantic ranking](semantic-ranking.md) | Uses the context or semantic meaning to compute a new relevance score. |
| [Semantic captions and highlights](semantic-how-to-query-request.md) | Sentences and phrases from a document that best summarize the content, with highlights over key passages for easy scanning. Captions that summarize a result are useful when individual content fields are too dense for the results page. Highlighted text elevates the most relevant terms and phrases so that users can quickly determine why a match was considered relevant. |
| [Semantic answers](semantic-answers.md) | An optional and additional substructure returned from a semantic query. It provides a direct answer to a query that looks like a question. |

### Order of operations

Components of semantic search extend the existing query execution pipeline in both directions. If you enable spelling correction, the [speller](speller-how-to-add.md) corrects typos at query onset, before terms reach the search engine.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantic components in query execution" border="true":::

Query execution proceeds as usual, with term parsing, analysis, and scans over the inverted indexes. The engine retrieves documents using token matching, and scores the results using the [default similarity scoring algorithm](index-similarity-and-scoring.md#similarity-ranking-algorithms). Scores are calculated based on the degree of linguistic similarity between query terms and matching terms in the index. If you defined them, scoring profiles are also applied at this stage. Results are then passed to the semantic search subsystem.

In the preparation step, the document corpus returned from the initial result set is analyzed at the sentence and paragraph level to find passages that summarize each document. In contrast with keyword search, this step uses machine reading and comprehension to evaluate the content. Through this stage of content processing, a semantic query returns [captions](semantic-how-to-query-request.md) and [answers](semantic-answers.md). To formulate them, semantic search uses language representation to extract and highlight key passages that best summarize a result. If the search query is a question - and answers are requested - the response will also include a text passage that best answers the question, as expressed by the search query. 

For both captions and answers, existing text is used in the formulation. The semantic models do not compose new sentences or phrases from the available content, nor does it apply logic to arrive at new conclusions. In short, the system will never return content that doesn't already exist.

Results are then re-scored based on the [conceptual similarity](semantic-ranking.md) of query terms.

To use semantic capabilities in queries, you'll need to make small modifications to the [search request](semantic-how-to-query-request.md), but no extra configuration or reindexing is required.

## Availability and pricing

Semantic capabilities are available through [sign-up registration](https://aka.ms/SemanticSearchPreviewSignup), on search services created at a Standard tier (S1, S2, S3), located in one of these regions: North Central US, West US, West US 2, East US 2, North Europe, West Europe. 

Spell correction is available in the same regions, but has no tier restrictions. If you have an existing service that meets tier and region criteria, only sign up is required.

Between preview launch on March 2 through late April, spell correction and semantic ranking are offered free of charge. Later in April the computational costs of running this functionality will become a billable event. The expected cost is about USD $500/month for 250,000 queries. You can find detailed cost information documented in the [Cognitive Search pricing page](https://azure.microsoft.com/pricing/details/search/) and in [Estimate and manage costs](search-sku-manage-costs.md).

## Next steps

[Sign-up](https://aka.ms/SemanticSearchPreviewSignup) for the preview on a search service that meets the tier and regional requirements noted in the previous section.

When your service is ready, [create a semantic query](semantic-how-to-query-request.md) to see semantic ranking in action.