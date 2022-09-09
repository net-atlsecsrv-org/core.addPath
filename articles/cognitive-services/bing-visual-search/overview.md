---
title: What is the Bing Visual Search API?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search provides details or insights about an image such as similar images or shopping sources.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 12/19/2019
ms.author: scottwhi
---

# What is the Bing Visual Search API?

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](https://aka.ms/cogsvcs/bingmove).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

The Bing Visual Search API returns insights for an image. You can either upload an image or provide a URL to one. Insights are visually similar images, shopping sources, webpages that include the image, and more. Insights returned by the Bing Visual Search API are similar to ones shown on Bing.com/images. 

If you use the [Bing Image Search API](../bing-image-search/overview.md), you can use insight tokens from that API's search results for your Bing Visual Search instead of uploading an image.

> [!IMPORTANT]
> If you get image insights using the Bing Image Search API, consider switching to the Bing Visual Search API, which provides more comprehensive insights.

## Insights

You can discover the following insights by using Bing Visual Search:

| Insight                              | Description |
|--------------------------------------|-------------|
| Visually similar images              | A list of images that are visually similar to the input image. |
| Visually similar products            | Products that are visually similar to the product shown.            |
| Shopping sources                     | Places where you can buy the item shown in the input image.            |
| Related searches                     | Related searches made by others or that are based on the contents of the image.            |
| Webpages that include the image     | Webpages that include the input image.            |
| Recipes                              | Webpages that include recipes for making the dish shown in the input image.            |
| Entities                             | Well-known people, places, and things. |

In addition to insights, Bing Visual Search returns a variety of terms (that is, tags) derived from the input image. The tags enable users to explore concepts found in the image. For example, if the input image is of a famous athlete, one of the tags may be the name of the athlete, another tag could be Sports. Or, if the input image is of an apple pie, the tags could be Apple Pie, Pies, and Desserts.

Bing Visual Search results also include bounding boxes for regions of interest in the image. For example, if the image contains several celebrities, the results may include bounding boxes for each of the recognized celebrities. Or, if Bing recognizes a product or clothing in the image, the result may include a bounding box for the recognized item.

## Workflow

The Bing Visual Search API is a RESTful web service, making it easy to call from any programming language that can make HTTP requests and parse JSON. You can use either the REST API or the SDK for the service.

1. Create a [Cognitive Services account](../cognitive-services-apis-create-account.md) to access the Bing Search APIs. If you don't have an Azure subscription, you can [create an account for free](https://azure.microsoft.com/free/cognitive-services/).
2. Send a request to the API with a valid search query.
3. Process the API response by parsing the returned JSON message.

## Next steps

First, try the Bing Visual Search API [interactive demo](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/).
The demo shows how you can quickly customize a search query and scour the web for images.

To get started quickly with your first request, see the quickstarts:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)

## See also

* The [Images - Visual Search](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) reference describes definitions and information on the endpoints, request headers, responses, and query parameters that you can use to request image-based search results.

* The [Bing Search API use and display requirements](../bing-web-search/use-display-requirements.md) specify acceptable uses of the content and information gained through the Bing search APIs.

* Visit the [Bing Search API hub page](../bing-web-search/overview.md) to explore the other available APIs.