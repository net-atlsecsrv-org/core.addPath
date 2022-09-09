---
title: Language support - Text Analytics API
titleSuffix: Azure Cognitive Services
description: "A list of natural languages supported by the Text Analytics API. This article explains which languages are supported for each operation: sentiment analysis, key phrase extraction, language detection, and entity recognition."
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
---
# Language and region support for the Text Analytics API

This article explains which languages are supported for each operation: sentiment analysis, key phrase extraction, language detection and named entity recognition.

## Language Detection

The Text Analytics API can detect up to 120 different languages. Language Detection returns the "script" of a language. For instance, for the phrase "I have a dog" it will return  `en` instead of  `en-US`. The only special case is Chinese, where the language detection capability will return `zh_CHS` or `zh_CHT` if it can determine the script given the text provided. In situations where a specific script cannot be identified for a Chinese document, it will return simply `zh`.

## Sentiment Analysis, Key Phrase Extraction, and Named Entity Recognition

For sentiment analysis, key phrase extraction, and entity recognition, the list of supported languages is more selective as the analyzers are refined to accommodate the linguistic rules of additional languages.

## Language list and status

Language support is initially rolled out in preview, graduating to generally available (GA) status, independently of each other and of the Text Analytics service overall. It's possible for languages to remain in preview, even while Text Analytics API transitions to generally available.

| Language    | Language code | Sentiment | Key phrases | Named Entity Recognition |   Notes  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabic      | `ar`          |           |             | ✔ \*                     | |
| Czech       | `cs`          |           |             | ✔ \*                     | |
| Chinese-Simplified | `zh-CN`|           |             | ✔ \*        |    |
| Danish      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Dutch       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| English     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finnish     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| French      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| German      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Greek       | `el`          | ✔ \*     |             |            |     |
| Hungarian   | `hu`          |           |             |  ✔ \*          |     | 
| Italian     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japanese    | `ja`          |          | ✔           |  ✔ \*          |     |
| Korean      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norwegian  (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polish      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portuguese (Portugal) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` also accepted|
| Portuguese (Brazil)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russian     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spanish     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Swedish     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turkish     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Language support is in preview

\*\* Named Entity Recognition and [Entity linking](how-tos/text-analytics-how-to-entity-linking.md) are both available for this language.    

## See also

[Cognitive Services Documentation page](https://docs.microsoft.com/azure/cognitive-services/)   
[Cognitive Services Product page](https://azure.microsoft.com/services/cognitive-services/)
