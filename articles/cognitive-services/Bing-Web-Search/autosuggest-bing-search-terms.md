---
title: Autosuggest search terms - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Pair the Bing Web Search API with the Bing Autosuggest API to provide users with an enhanced search experience.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
---

# Autosuggest Bing search terms in your application

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](https://aka.ms/cogsvcs/bingmove).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

If you provide a search box where the user enters their search term, use the [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) to improve the experience. The API returns suggested query strings based on partial search terms as the user types.

After the user enters a search term, it must be URL encoded before the [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) query parameter is set. For example, if the user enters *sailing dinghies*, set `q` to `sailing+dinghies` or `sailing%20dinghies`.

If the query term contains a spelling mistake, the search response includes a [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) object. The object shows the original spelling and the corrected spelling that Bing used for the search.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

You can use this information to let the user know that you modified their query string when you display the search results.

![Query context UX example](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## Next steps  

* Review sample [Bing Web Search API responses](search-responses.md).  

## See also  

* [Bing Web Search API reference](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)