---
title: Call the Text Analytics API
titleSuffix: Azure Cognitive Services
description: This article explains how you can call the Azure Cognitive Services Text Analytics REST API and Postman.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
---

# How to call the Text Analytics REST API

Calls to the **Text Analytics API** are HTTP POST/GET calls, which you can formulate in any language. In this article, we use REST and [Postman](https://www.postman.com/downloads/) to demonstrate key concepts.

Each request must include your access key and an HTTP endpoint. The endpoint specifies the region you chose during sign up, the service URL, and a resource used on the request: `sentiment`, `keyphrases`, `languages`, and `entities`. 

Recall that Text Analytics is stateless so there are no data assets to manage. Your text is uploaded, analyzed upon receipt, and results are returned immediately to the calling application.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## Prerequisites

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## JSON schema definition

Input must be JSON in raw unstructured text. XML is not supported. The schema is simple, consisting of the elements described in the following list. 

You can currently submit the same documents for all Text Analytics operations: sentiment, key phrase, language detection, and entity identification. (The schema is likely to vary for each analysis in the future.)

| Element | Valid values | Required? | Usage |
|---------|--------------|-----------|-------|
|`id` |The data type is string, but in practice document IDs tend to be integers. | Required | The system uses the IDs you provide to structure the output. Language codes, key phrases, and sentiment scores are generated for each ID in the request.|
|`text` | Unstructured raw text, up to 5,120 characters. | Required | For language detection, text can be expressed in any language. For sentiment analysis, key phrase extraction and entity identification, the text must be in a [supported language](../text-analytics-supported-languages.md). |
|`language` | 2-character [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) code for a [supported language](../text-analytics-supported-languages.md) | Varies | Required for sentiment analysis, key phrase extraction, and entity linking; optional for language detection. There is no error if you exclude it, but the analysis is weakened without it. The language code should correspond to the `text` you provide. |

For more information about limits, see [Text Analytics Overview > Data limits](../overview.md#data-limits). 


```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    },
    {
      "language": "en",
      "id": "3",
      "text": "Pike place market is my favorite Seattle attraction."
    }
  ]
}
```


## Set up a request in Postman

The service accepts request up to 1 MB in size. If you are using Postman (or another Web API test tool), set up the endpoint to include the resource you want to use, and provide the access key in a request header. Each operation requires that you append the appropriate resource to the endpoint. 

1. In Postman:

   + Choose **Post** as the request type.
   + Paste in the endpoint you copied from the portal page.
   + Append a resource.

   Resource endpoints are as follows (your region may vary):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v3.0/entities/recognition/general`

2. Set the three request headers:

   + `Ocp-Apim-Subscription-Key`: your access key, obtained from Azure portal.
   + `Content-Type`: application/json.
   + `Accept`: application/json.

   Your request should look similar to the following screenshot, assuming a **/keyPhrases** resource.

   ![Request screenshot with endpoint and headers](../media/postman-request-keyphrase-1.png)

4. Click **Body** and choose **raw** for the format.

   ![Request screenshot with body settings](../media/postman-request-body-raw.png)

5. Paste in some JSON documents in a format that is valid for the intended analysis. For more information about a particular analysis, see the topics below:

  + [Language detection](text-analytics-how-to-language-detection.md)  
  + [Key phrase extraction](text-analytics-how-to-keyword-extraction.md)  
  + [Sentiment analysis](text-analytics-how-to-sentiment-analysis.md)  
  + [Entity recognition](text-analytics-how-to-entity-linking.md)  


6. Click **Send** to submit the request. See the [data limits](../overview.md#data-limits) section in the overview for information on the number of requests you can send per minute and second.

   In Postman, the response is displayed in the next window down, as a single JSON document, with an item for each document ID provided in the request.

## See also 

 [Text Analytics Overview](../overview.md)  
 [Frequently asked questions (FAQ)](../text-analytics-resource-faq.md)

## Next steps

> [!div class="nextstepaction"]
> [Detect language](text-analytics-how-to-language-detection.md)
