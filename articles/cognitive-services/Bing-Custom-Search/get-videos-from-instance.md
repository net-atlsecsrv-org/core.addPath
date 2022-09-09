---
title: Get videos from your custom view - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: High-level overview about using Bing Custom Search to get videos from your custom view of the Web.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
---

# Get videos from your custom view

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](https://aka.ms/cogsvcs/bingmove).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

Bing Custom Videos Search lets you enrich your custom search experience with videos. Similar to web results, custom search supports searching for videos in your instance's list of websites. You can get the videos using Bing's Custom Videos Search API or through the Hosted UI feature. Using the Hosted UI feature is simple to use and recommended for getting your search experience up and running in short order. For information about configuring your Hosted UI to include videos, see [Configure your hosted UI experience](hosted-ui.md).

If you want more control over displaying the search results, you can use Bing's Custom Videos Search API. Because calling the API is similar to calling the Bing Video Search API, checkout [Bing Video Search](../bing-video-search/overview.md) for examples calling the API. But before you do that, familiarize yourself with the [Custom Videos Search API reference](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) content. The main differences are the supported query parameters (you must include the customConfig query parameter) and the endpoint you send requests to.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->