---
title: Examples of Bing insights - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: This article contains examples of how Bing Visual Search might use and display image insights on Bing.com.
services: cognitive-services
author: swhite-msft
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
---

# Examples of Bing insights usage

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](https://aka.ms/cogsvcs/bingmove).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](https://aka.ms/cogsvcs/bingmigration).

This article contains examples of how Bing might use and display image insights on Bing.com.

## PagesIncluding insight example

The following displays a link to the first webpage and lets the user expand and collapse the list of other webpages that include the image:

![Expanded pages including](./media/pages-including.PNG)

## ShoppingSources insight example

The following shows how Bing might display shopping sources for products seen in the image:

![Shopping sources](./media/shopping-sources.PNG)

## VisualSearch insight example

The following shows how Bing might display visually similar images (see **Related images** in the example):

![Visually similar images](./media/similar-images.PNG)

## Recipes insight example

The following shows how Bing might display recipes for the food shown in the image. The example lets the user know there are recipes available:

![Recipes and pages including](./media/recipes-pages-including.PNG)

 And provides the link to the recipes when the user expands the list:

![Expanded recipe pages including](./media/expanded-recipes-pages-including.PNG)

## RelatedSearches insight example

The following shows how Bing might display related searches of images made by others. If the user clicks the image, the user is taken to the Bing.com/images search results page for that related query.

![Related searches for images](./media/bordered-related-searches.PNG)

## Entity insight example

The following shows how Bing might display information about the entity (person, place, or thing) shown in the image. If the user clicks the entity link, the user is taken to the Bing.com search results page for the entity:

![Entity shown in image](./media/entity.PNG)

## Displaying other insights that the user might explore

The following shows how Bing might display other information about the image that the user can explore.

![Explore other insights about the image](./media/apple-pie-more-tags.PNG)

## Bounding boxes and hot spots

Non-default tags include the bounding box that identifies the area of interest in the image that the tag applies to. If the bounding box does not identify the entire image, use the bounding box to create a hot spot on the image. The user can click the hot spot to get information related to the content found under the hot spot (or rectangle). For example, if the image is a high-fashion image, the results may contain tags (and bounding boxes) for accessories shown in the image, such as a purse, jewelry, scarfs, and so on. The following example shows a hot-spot rectangle for the sunglasses shown in the image:

![Bounding box and hot spot](./media/click-to-search.PNG)

## Next steps

To get started with your first request, see the quickstarts:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)





