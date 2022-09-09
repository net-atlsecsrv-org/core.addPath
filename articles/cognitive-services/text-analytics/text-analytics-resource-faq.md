---
title: Frequently Asked Questions about the Text Analytics API
titleSuffix: Azure Cognitive Services
description: Find answers to commonly asked questions about concepts, code, and scenarios related to the Text Analytics API for Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
---
# Frequently Asked Questions (FAQ) about the Text Analytics API

 Find answers to commonly asked questions about concepts, code, and scenarios related to the Text Analytics API in Azure Cognitive Services.

## What is the maximum size and number of requests I can make to the API?

See the [data limits](concepts/data-limits.md) article for information on the size and number of requests you can send per minute and second.

## Can Text Analytics identify sarcasm?

Analysis is for positive-negative sentiment rather than mood detection.

There is always some degree of imprecision in sentiment analysis, but the model is most useful when there is no hidden meaning or subtext to the content. Irony, sarcasm, humor, and similarly nuanced content rely on cultural context and norms to convey intent. This type of content is among the most challenging to analyze. Typically, the greatest discrepancy between a given score produced by the analyzer and a subjective assessment by a human is for content with nuanced meaning.

## Can I add my own training data or models?

No, the models are pretrained. The only operations available on uploaded data are scoring, key phrase extraction, and language detection. We do not host custom models. If you want to create and host custom machine learning models, consider the [machine learning capabilities in Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## Can I request additional languages?

Sentiment analysis and key phrase extraction are available for a [select number of languages](./language-support.md). Natural language processing is complex and requires substantial testing before new functionality can be released. For this reason, we avoid pre-announcing support so that no one takes a dependency on functionality that needs more time to mature. 

To help us prioritize which languages to work on next, vote for specific languages using the [feedback tool](https://feedback.azure.com/forums/932041-azure-cognitive-services?category_id=395749). 

## Why does key phrase extraction return some words but not others?

Key phrase extraction eliminates non-essential words and standalone adjectives. Adjective-noun combinations, such as "spectacular views" or "foggy weather" are returned together.

Generally, output consists of nouns and objects of the sentence. Output is listed in order of importance, with the first phrase being the most important. Importance is measured by the number of times a particular concept is mentioned, or the relation of that element to other elements in the text.

## Why does output vary, given identical inputs?

Improvements to models and algorithms are announced if the change is major, or quietly slipstreamed into the service if the update is minor. Over time, you might find that the same text input results in a different sentiment score or key phrase output. This is a normal and intentional consequence of using managed machine learning resources in the cloud.

## Service availability and redundancy

### Is Text Analytics service zone resilient?

Yes. The Text Analytics service is zone-resilient by default.

### How do I configure the Text Analytics service to be zone-resilient?

No customer configuration is necessary to enable zone-resiliency. Zone-resiliency for Text Analytics resources is available by default and managed by the service itself.

## Next steps

Is your question about a missing feature or functionality? Consider requesting or voting for it using the [feedback tool](https://feedback.azure.com/forums/932041-azure-cognitive-services?category_id=395749).

## See also

 * [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
