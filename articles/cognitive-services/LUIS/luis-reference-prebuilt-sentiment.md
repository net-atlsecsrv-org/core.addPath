---
title: Sentiment analysis - LUIS
titleSuffix: Azure Cognitive Services
description: If Sentiment analysis is configured, the LUIS json response includes sentiment analysis.
services: cognitive-services

manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/06/2021

---
# Sentiment analysis
If Sentiment analysis is configured, the LUIS json response includes sentiment analysis. Learn more about sentiment analysis in the [Text Analytics](../text-analytics/index.yml) documentation.

LUIS uses Text Analytics V2. 

Sentiment Analysis is configured when publishing your application. See [how to publish an app](./luis-how-to-publish-app.md) for more information.

## Resolution for sentiment

Sentiment data is a score between 1 and 0 indicating the positive (closer to 1) or negative (closer to 0) sentiment of the data.

#### [English language](#tab/english)

When culture is `en-us`, the response is:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### [Other languages](#tab/other-languages)

For all other cultures, the response is:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## Next steps

Learn more about the [V3 prediction endpoint](luis-migration-api-v3.md).
