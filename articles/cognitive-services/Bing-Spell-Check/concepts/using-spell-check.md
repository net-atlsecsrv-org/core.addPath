---
title: Using the Bing Spell Check API
titleSuffix: Azure Cognitive Services
description: Learn about the Bing Spell Check modes, settings, and other information related to the API.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
---

# Using the Bing Spell Check API

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use this article to learn about using the Bing Spell Check API to perform contextual grammar and spell checking. While most spell-checkers rely on dictionary-based rule sets, the Bing spell-checker leverages machine learning and statistical machine translation to provide accurate and contextual corrections. 

## Spell check modes

The API supports two proofing modes, `Proof` and `Spell`.  Try examples [here](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### Proof - for documents 

The default mode is `Proof`. The `Proof` spelling mode provides the most comprehensive checks,  adding capitalization, basic punctuation, and other features to aid document creation. but it is available only in the en-US (English-United States), es-ES(Spanish), pt-BR(Portuguese) markets (Note: only in beta version for Spanish and Portuguese). For all other markets, set the mode query parameter to Spell. 

> [!NOTE]
> If the length of query text exceeds 4096, it will be truncated to 4096 characters, then get processed. 

### Spell -  for web searches/queries

`Spell` is more aggressive in order to return better search results. The `Spell` mode finds most spelling mistakes but doesn't find some of the grammar errors that `Proof` catches, for example, capitalization and repeated words.

> [!NOTE]
> * The maximum supported query length is below. If the query exceeds the max length, the query and its results will not be altered.
>    * 130 characters for the following language codes: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 characters for all others.
> * The Spell mode does not support square bracket characters (`[` and `]`) in queries, and may cause inconsistent results. We recommend removing them from your queries when using the Spell mode.

## Market setting

A [market code](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) should be specified with the `mkt` query parameter in your request. The API will otherwise use a default market based on the request's IP address.


## HTTP POST and GET support

The API supports either HTTP POST or HTTP GET. Which you use depends on the length of text you plan to proof. If the strings are always less than 1,500 characters, you'd use a GET. But if you want to support strings up to 10,000 characters, you'd use POST. The text string may include any valid UTF-8 character.

The following example shows a POST request to check the spelling and grammar of a text string. The example includes the [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) query parameter for completeness (it could have been left out since `mode` defaults to Proof). The [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) query parameter contains the string to be proofed.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

If you use HTTP GET, you'd include the `text` query parameter in the URL's query string
  
The following shows the response to the previous request. The response contains a [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) object. 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
The [flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) field lists the spelling and grammar errors that the API found in the [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) string. The `token` field contains the word to be replaced. You'd use the zero-based offset in the `offset` field to find the token in the `text` string. You'd then replace the word at that location with the word in the `suggestion` field. 

If the `type` field is RepeatedToken, you'd still replace the token with `suggestion` but you'd also likely need to remove the trailing space.

## Throttling requests

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## Next steps

- [What is the Bing Spell Check API?](../overview.md)
- [Bing Spell Check API v7 Reference](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)