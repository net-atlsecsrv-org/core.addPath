---
title: What's new in the Text Analytics API
titleSuffix: Text Analytics - Azure Cognitive Services
description: This article provides you with information about new releases and features for the Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: aahi
---

# What's new in the Text Analytics API?

The Text Analytics API is updated on an ongoing basis. To stay up-to-date with recent developments, this article provides you with information about new releases and features.

## October 2020

* Hindi support for Sentiment Analysis v3.x, starting with model version `2020-04-01`. 
* Model verion `2020-09-01` for the v3 /languages endpoint, which adds increased language detection and accuracy improvements.
* v3 availability in Central India and UAE North.

## September 2020

### General API updates

* Release of a new URL for the Text Analytics v3.1 public preview to support updates to the following Named Entity Recognition v3 endpoints: 
    * `/pii` endpoint now includes the new `redactedText` property in the response JSON where detected PII entities in the input text are replaced by an `*` for each character of those entities.
    * `/linking` endpoint now includes the `bingID` property in the response JSON for linked entities.
* The following Text Analytics preview API endpoints were retired on September 4th, 2020:
    * v2.1-preview
    * v3.0-preview
    * v3.0-preview.1
    
> [!div class="nextstepaction"]
> [Learn more about Text Analytics API v3.1-Preview.2](quickstarts/text-analytics-sdk.md)

### Text Analytics for health container updates

The following updates are specific to the September release of the Text Analytics for health container only.
* A new container image with tag `1.1.013530001-amd64-preview` with the new model-version `2020-09-03` has been released to the containerpreview repository. 
* This model version provides improvements in entity recognition, abbreviation detection, and latency enhancements.

> [!div class="nextstepaction"]
> [Learn more about Text Analytics for health](how-tos/text-analytics-for-health.md)

## August 2020

### General API updates

* Model version `2020-07-01` for the v3 `/keyphrases`, `/pii` and `/languages` endpoints, which adds:
    * Additional government and country specific [entity categories](named-entity-types.md?tabs=personal) for Named Entity Recognition.
    * Norwegian and Turkish support in Sentiment Analysis v3.
* An HTTP 400 error will now be returned for v3 API requests that exceed the published [data limits](concepts/data-limits.md). 
* Endpoints that return an offset now support the optional `stringIndexType` parameter, which adjusts the returned `offset` and `length` values to match a supported [string index scheme](concepts/text-offsets.md).

### Text Analytics for health container updates

The following updates are specific to the August release of the Text Analytics for health container only.

* New model-version for Text Analytics for health: `2020-07-24`
* New URL for sending Text Analytics for health requests: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Please note that a browser cache clearing will be needed in order to use the demo web app included in this new container image)

The following properties in the JSON response have changed:

* `type` has been renamed to `category` 
* `score` has been renamed to `confidenceScore`
* Entities in the `category` field of the JSON output are now in pascal case. The following entities have been renamed:
    * `EXAMINATION_RELATION` has been renamed to `RelationalOperator`.
    * `EXAMINATION_UNIT` has been renamed to `MeasurementUnit`.
    * `EXAMINATION_VALUE` has been renamed to `MeasurementValue`.
    * `ROUTE_OR_MODE` has been renamed `MedicationRoute`.
    * The relational entity `ROUTE_OR_MODE_OF_MEDICATION` has been renamed to `RouteOfMedication`.

The following entities have been added:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Relation extraction
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Learn more about Text Analytics for health container](how-tos/text-analytics-for-health.md)

## July 2020 

### Text Analytics for health container - Public gated preview

The Text Analytics for health container is now in public gated preview, which lets you extract information from unstructured English-language text in clinical documents such as: patient intake forms, doctor's notes, research papers and discharge summaries. Currently, you will not be billed for Text Analytics for health container usage.

The container offers the following features:

* Named Entity Recognition
* Relation extraction
* Entity linking
* Negation

## May 2020

### Text Analytics API v3 General Availability

Text Analysis API v3 is now generally available with the following updates:

* Model version `2020-04-01`
* New [data limits](concepts/data-limits.md) for each feature
* Updated [language support](language-support.md) for [Sentiment Analysis (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Separate endpoint for Entity Linking 
* New "Address" entity category in [Named Entity Recognition (NER) v3](how-tos/text-analytics-how-to-entity-linking.md).
* New subcategories in NER v3:
   * Location - Geographical
   * Location - Structural
   * Organization - Stock Exchange
   * Organization - Medical
   * Organization - Sports
   * Event - Cultural
   * Event - Natural
   * Event - Sports

The following properties in the JSON response have been added:
   * `SentenceText` in Sentiment Analysis
   * `Warnings` for each document 

The names of the following properties in the JSON response have been changed, where applicable:

* `score` has been renamed to `confidenceScore`
    * `confidenceScore` has two decimal points of precision. 
* `type` has been renamed to `category`
* `subtype` has been renamed to `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Learn more about Text Analytics API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### Text Analytics API v3.1 Public Preview
   * New Sentiment Analysis feature - [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * New [Personal (`PII`) domain filter](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) for protected health information (`PHI`).

> [!div class="nextstepaction"]
> [Learn more about Text Analytics API v3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## February 2020

### SDK support for Text Analytics API v3 Public Preview

As part of the [unified Azure SDK release](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), the Text Analytics API v3 SDK is now available as a public preview for the following programming languages:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Learn more about Text Analytics API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### Named Entity Recognition v3 public preview

Additional entity types are now available in the Named Entity Recognition (NER) v3 public preview service as we expand the detection of general and personal information entities found in text. This update introduces [model version](concepts/model-versioning.md) `2020-02-01`, which includes:

* Recognition of the following general entity types (English only):
    * PersonType
    * Product
    * Event
    * Geopolitical Entity (GPE) as a subtype under Location
    * Skill

* Recognition of the following personal information entity types (English only):
    * Person
    * Organization
    * Age as a subtype under Quantity
    * Date as a subtype under DateTime
    * Email 
    * Phone Number (US only)
    * URL
    * IP Address

> [!div class="nextstepaction"]
> [Learn more about Named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### October 2019

#### Named Entity Recognition (NER)

* A [new endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) for recognizing personal information entity types (English only)

* Separate endpoints for [entity recognition](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) and [entity linking](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Model version](concepts/model-versioning.md) `2019-10-01`, which includes:
    * Expanded detection and categorization of entities found in text. 
    * Recognition of the following new entity types:
        * Phone number
        * IP address

Entity linking supports English and Spanish. NER language support varies by the entity type.

#### Sentiment Analysis v3 public preview

* A [new endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) for analyzing sentiment.
* [Model version](concepts/model-versioning.md) `2019-10-01`, which includes:

    * Significant improvements in the accuracy and detail of the API's text categorization and scoring.
    * Automatic labeling for different sentiments in text.
    * Sentiment analysis and output on a document and sentence level. 

It supports English (`en`), Japanese (`ja`), Chinese Simplified (`zh-Hans`),  Chinese Traditional (`zh-Hant`), French (`fr`), Italian (`it`), Spanish (`es`), Dutch (`nl`), Portuguese (`pt`), and German (`de`), and is available in the following regions: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, and `West US 2`. 

> [!div class="nextstepaction"]
> [Learn more about Sentiment Analysis v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## Next steps

* [What is the Text Analytics API?](overview.md)  
* [Example user scenarios](text-analytics-user-scenarios.md)
* [Sentiment analysis](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Language detection](how-tos/text-analytics-how-to-language-detection.md)
* [Entity recognition](how-tos/text-analytics-how-to-entity-linking.md)
* [Key phrase extraction](how-tos/text-analytics-how-to-keyword-extraction.md)
