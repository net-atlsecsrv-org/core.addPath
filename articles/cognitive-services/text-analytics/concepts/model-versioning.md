---
title: Specify the model version in Text Analytics v3
titleSuffix: Azure Cognitive Services
description: Learn how to specify the Text Analytics API model used on your data. 
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
---

# Model versioning in the Text Analytics API

Version 3 of the Text Analytics API lets you choose the model version that gets used on your data. Use the optional `model-version` parameter to select the version of the model in your API requests. For example: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. If this parameter isn't specified the API will default to the latest stable version. 

## Available versions

Use the table below to find which model versions are supported by each endpoint.


| Endpoint                        | Supported Versions                       | latest version |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


You can find details about the updates for these models in [What's new](../whats-new.md).

## Text Analytics for Health

The [Text Analytics for Health](../how-tos/text-analytics-for-health.md) container uses separate model versioning than the above API endpoints.  Please note that only one model version is available per container image.

| Endpoint                        | Container Image Tag                     | Model version |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview` or latest | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## Next steps

* [Text Analytics overview](../overview.md)
* [Sentiment analysis](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entity recognition](../how-tos/text-analytics-how-to-entity-linking.md)
