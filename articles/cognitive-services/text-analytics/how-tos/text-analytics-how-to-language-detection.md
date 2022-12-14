---
title: Detect language with the Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Detect language by using the Text Analytics REST API from Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/02/2021
ms.author: aahi
---

# Example: Detect language with Text Analytics

The [Language Detection](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) feature of the Azure Text Analytics REST API evaluates text input for each document and returns language identifiers with a score that indicates the strength of the analysis.

This capability is useful for content stores that collect arbitrary text, where language is unknown. You can parse the results of this analysis to determine which language is used in the input document. The response also returns a score that reflects the confidence of the model. The score value is between 0 and 1.

The Language Detection feature can detect a wide range of languages, variants, dialects, and some regional or cultural languages. The exact list of languages for this feature isn't published.

If you have content expressed in a less frequently used language, you can try the Language Detection feature to see if it returns a code. The response for languages that can't be detected is `unknown`.

> [!TIP]
> Text Analytics also provides a Linux-based Docker container image for language detection, so you can [install and run the Text Analytics container](text-analytics-how-to-install-containers.md) close to your data.

## Preparation

You must have JSON documents in this format: ID and text.

The document size must be under 5,120 characters per document. You can have up to 1,000 items (IDs) per collection. The collection is submitted in the body of the request. The following sample is an example of content you might submit for language detection:

```json
{
    "documents": [
        {
            "id": "1",
            "text": "This document is in English."
        },
        {
            "id": "2",
            "text": "Este documento est?? en ingl??s."
        },
        {
            "id": "3",
            "text": "Ce document est en anglais."
        },
        {
            "id": "4",
            "text": "??????????????????"
        },
        {
            "id": "5",
            "text": "???????? ???????????????? ???? ???????????????????? ??????????."
        }
    ]
}
```

## Step 1: Structure the request

For more information on request definition, see [Call the Text Analytics API](text-analytics-how-to-call-api.md). The following points are restated for convenience:

+ Create a POST request. To review the API documentation for this request, see the [Language Detection API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Set the HTTP endpoint for language detection. Use either a Text Analytics resource on Azure or an instantiated [Text Analytics container](text-analytics-how-to-install-containers.md). You must include `/text/analytics/v3.0/languages` in the URL. For example: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Set a request header to include the [access key](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) for Text Analytics operations.

+ In the request body, provide the JSON documents collection you prepared for this analysis.

> [!Tip]
> Use [Postman](text-analytics-how-to-call-api.md) or open the **API testing console** in the [documentation](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) to structure a request and POST it to the service.

## Step 2: POST the request

Analysis is performed upon receipt of the request. For information on the size and number of requests you can send per minute and second, see the [data limits](../concepts/data-limits.md) article.

Recall that the service is stateless. No data is stored in your account. Results are returned immediately in the response.


## Step 3: View the results

All POST requests return a JSON-formatted response with the IDs and detected properties.

Output is returned immediately. You can stream the results to an application that accepts JSON or save the output to a file on the local system. Then, import the output into an application that you can use to sort, search, and manipulate the data.

Results for the example request should look like the following JSON document. Notice that it's one JSON document with multiple items with each item representing the detection result for every document you sumbit. Output is in English. 

Language detection will return one predominant language for one document, along with it's [ISO 639-1](https://www.iso.org/standard/22109.html) name, friendly name and confidence score. A positive score of 1.0 expresses the highest possible confidence level of the analysis.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.99,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"2",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"3",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"zh_chs",
                "name":"Chinese_Simplified"
            },
            "id":"4",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"ru",
                "name":"Russian"
            },
            "id":"5",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### Ambiguous content

In some cases it may be hard to disambiguate languages based on the input. You can use the `countryHint` parameter to specify an [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country/region code. By default the API is using the "US" as the default countryHint, to remove this behavior you can reset this parameter by setting this value to empty string `countryHint = ""` .

For example, "Impossible" is common to both English and French and if given with limited context the response will be based on the "US" country/region hint. If the origin of the text is known to be coming from France that can be given as a hint.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

The service now has additional context to make a better judgment: 

**Output**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

If the analyzer can't parse the input, it returns `(Unknown)`. An example is if you submit a text block that consists solely of Arabic numerals.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.0,
                "iso6391Name":"(Unknown)",
                "name":"(Unknown)"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### Mixed-language content

Mixed-language content within the same document returns the language with the largest representation in the content, but with a lower positive rating. The rating reflects the marginal strength of the assessment. In the following example, input is a blend of English, Spanish, and French. The analyzer counts characters in each segment to determine the predominant language.

**Input**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ??Se ofrecen clases en espa??ol? Es mi primera lengua y m??s f??cil para escribir. Que diriez-vous des cours en fran??ais?"
        }
    ]
}
```

**Output**

The resulting output consists of the predominant language, with a score of less than 1.0, which indicates a weaker level of confidence.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.94,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

## Summary

In this article, you learned concepts and workflow for language detection by using Text Analytics in Azure Cognitive Services. The following points were explained and demonstrated:

+ [Language detection](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) is available for a wide range of languages, variants, dialects, and some regional or cultural languages.
+ JSON documents in the request body include an ID and text.
+ The POST request is to a `/languages` endpoint by using a personalized [access key and an endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) that's valid for your subscription.
+ Response output consists of language identifiers for each document ID. The output can be streamed to any app that accepts JSON. Example apps include Excel and Power BI, to name a few.

## See also

* [Text Analytics overview](../overview.md)
* [Using the Text Analytics client library](../quickstarts/client-libraries-rest-api.md)
* [What's new](../whats-new.md)
