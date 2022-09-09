---
title: Suggesting search terms with the Bing Autosuggest API
titleSuffix: Azure Cognitive Services
description: This article discusses the concept of suggesting query terms using the Bing Autosuggest API and the impact of query length on relevance.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
---

# Suggesting query terms

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](https://aka.ms/cogsvcs/bingmove).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

Typically, you'd call the Bing Autosuggest API each time a user types a new character in your application's search box. The completeness of the query string impacts the relevance of the suggested query terms that the API returns. The more complete the query string, the more relevant the list of suggested query terms are. For example, the suggestions that the API may return for `s` are likely to be less relevant than the queries it returns for `sailing dinghies`.

## Example request

The following example shows a request that returns the suggested query strings for *sail*. Remember to URL encode the user's partial query term when you set the [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) query parameter. For example, if the user entered *sailing les*, set `q` to `sailing+les` or `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

The following response contains a list of [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) objects that contain the suggested query terms.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## Using suggested query terms

Each suggestion includes a `displayText`, `query` and, `url` field. The `displayText` field contains the suggested query that you use to populate your search box's drop-down list. You must display all suggestions that the response includes, and in the given order.

The following example shows a drop-down search box with suggested query terms from the Bing Autosuggest API.

![Autosuggest drop-down search box list](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

If the user selects a suggested query from the drop-down list, you'd use the query term in the `query` field to call the [Bing Web Search API](../../bing-web-search/overview.md) and display the results yourself. Or, you could use the URL in the `url` field to send the user to the Bing search results page instead.

## Next steps

* [What is the Bing Autosuggest API?](../get-suggested-search-terms.md)