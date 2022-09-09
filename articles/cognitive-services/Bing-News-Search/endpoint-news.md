---
title: Bing News Search endpoints
titleSuffix: Azure Cognitive Services
description: This article provides a summary of the News search API endpoints; news, top news, and trending news.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
---

# Bing News Search API endpoints

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

The **News Search API** returns news articles, Web pages, images, videos, and [entities](../bing-entities-search/overview.md). Entities contain summary information about a person, place, or topic.

## Endpoints

To get news search results using the Bing News Search API, send a `GET` request to one of the following endpoints. The headers and URL parameters define further specifications.

### News items by search query

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Returns news items based on a search query. If the search query is empty, the API will return top news articles from different categories. Send a query by url encoding your search term and appending it to the`q=""` parameter. For availability, see [Supported countries/regions and markets](language-support.md#supported-markets-for-news-search-endpoint).

### Top news items by category

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Returns the top news items by category. You can specifically request the top business, sports, or entertainment articles using `category=business`, `category=sports`, or `category=entertainment`.  The `category` parameter can only be used with the `/news` URL. There are some formal requirements for specifying categories; refer to `category` in the [query parameter](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) documentation. Send a query by url encoding your search term and appending it to the`q=""` parameter. For availability, see [Supported countries/regions and markets](language-support.md#supported-markets-for-news-endpoint).

### Trending news topics 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Returns news topics that are currently trending on social networks. When the `/trendingtopics` option is included, Bing search ignores several other parameters, such as `freshness` and `?q=""`. For availability, see [Supported countries/regions and markets](language-support.md#supported-markets-for-news-trending-endpoint).

## Next steps

For details about headers, parameters, market codes, response objects, errors, etc., see the [Bing News search API v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) reference.

For complete information about the parameters supported by each endpoint, see the reference pages for each type.
For examples of basic requests using the News search API, see [Bing News Search Quick-starts](/azure/cognitive-services/bing-news-search).