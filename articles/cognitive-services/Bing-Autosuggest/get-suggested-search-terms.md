---
title: What is Bing Autosuggest?
titleSuffix: Azure Cognitive Services
description: The Bing Autosuggest API returns a list of suggested queries based on the partial query string in the search box.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
---
# What is Bing Autosuggest?

If your application sends queries to any of the Bing Search APIs, you can use the Bing Autosuggest API to improve your users' search experience. The Bing Autosuggest API returns a list of suggested queries based on the partial query string in the search box. As characters are entered into the search box, you can display suggestions in a drop-down list.

## Bing Autosuggest API features

| Feature                                                                                                                                                                                 | Description                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Suggest search terms in real-time](concepts/get-suggestions.md) | Improve your app experience by using the Autosuggest API to display suggested search terms as they're typed. |

## Workflow

The Bing Autosuggest API is a RESTful web service, easy to call from any programming language that can make HTTP requests and parse JSON. 

1. Create a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with access to the Bing Search APIs. If you don't have an Azure subscription, you can [create an account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) for free.
2. Send a request to this API each time a user types a new character in your application's search box.
3. Process the API response by parsing the returned JSON message.

Typically, you'd call this API each time the user types a new character in your application's search box. As more characters are entered, the API will return more relevant suggested search queries. For example, the suggestions the API might return for a single `s` are likely to be less relevant than ones for `sail`.

The following example shows a drop-down search box with suggested query terms from the Bing Autosuggest API.

![Autosuggest drop-down search box list](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

When a user selects a suggestion from the drop-down list, you can use it to begin searching with one of the Bing Search APIs, or directly go to the Bing search results page.

## Next steps

To get started quickly with your first request, see [Making Your First Query](quickstarts/csharp.md).

Familiarize yourself with the [Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) reference. The reference contains the list of endpoints, headers, and query parameters that you'd use to request suggested query terms, and the definitions of the response objects.

Learn how to search the web by using the [Bing Web Search API](../bing-web-search/search-the-web.md).

Be sure to read [Bing Use and Display Requirements](./useanddisplayrequirements.md) so you don't break any of the rules about using the search results.
