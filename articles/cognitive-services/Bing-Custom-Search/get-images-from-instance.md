---
title: Get images from your custom view - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: High-level overview about using Bing Custom Search to get images from your custom view of the Web.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
---

# Get images from your custom view

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Custom Images Search lets you enrich your custom search experience with images. Similar to web results, custom search supports searching for images in your instance's list of websites. You can get the images using Bing's Custom Images Search API or through the Hosted UI feature. Using the Hosted UI feature is simple to use and recommended for getting your search experience up and running in short order.  For information about configuring your Hosted UI to include images, see [Configure your hosted UI experience](hosted-ui.md).

If you want more control over displaying the search results, you can use Bing's Custom Images Search API. Because calling the API is similar to calling the Bing Image Search API, checkout [Bing Image Search](../Bing-Image-Search/overview.md) for examples calling the API. But before you do that, familiarize yourself with the [Custom Images Search API reference](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) content. The main differences are the supported query parameters (you must include the customConfig query parameter) and the endpoint you send requests to.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->