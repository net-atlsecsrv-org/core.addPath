---
title: Return a semantic answer
titleSuffix: Azure Cognitive Search
description: Describes the composition of a semantic answer and how to obtain answers from a result set.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
---

# Return a semantic answer in Azure Cognitive Search

> [!IMPORTANT]
> Semantic search is in public preview, available through the preview REST API only. Preview features are offered as-is, under [Supplemental Terms of Use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), and are not guaranteed to have the same implementation at general availability. These features are billable. For more information, see [Availability and pricing](semantic-search-overview.md#availability-and-pricing).

When formulating a [semantic query](semantic-how-to-query-request.md), you can optionally extract content from the top-matching documents that "answers" the query directly. One or more answers can be included in the response, which you can then render on a search page to improve the user experience of your app.

In this article, learn how to request a semantic answer, unpack the response, and learn what content characteristics are most conducive to producing high quality answers.

## Prerequisites

All prerequisites that apply to [semantic queries](semantic-how-to-query-request.md) also apply to answers, including service tier and region.

+ Query logic must include the semantic query parameters, plus the "answers" parameter. Required parameters are discussed in this article.

+ Query strings entered by the user must be formulated in language having the characteristics of a question (what, where, when, how).

+ Search documents must contain text having the characteristics of an answer, and that text must exist in one of the fields listed in "searchFields". For example, given a query "what is a hash table", if none of the searchFields contain passages that include "A hash table is ..." , then an answer is unlikely to be returned.

## What is a semantic answer?

A semantic answer is a substructure of a [semantic query response](semantic-how-to-query-request.md). It consists of one or more verbatim passages from a search document, formulated as an answer to a query that looks like a question. For an answer to be returned, phrases or sentences must exist in a search document that have the language characteristics of an answer, and the query itself must be posed as a question.

Cognitive Search uses a machine reading comprehension model to pick the best answer. The model produces a set of potential answers from the available content, and when it reaches a high enough confidence level, it will propose an answer.

Answers are returned as an independent, top-level object in the query response payload that you can choose to render on  search pages, along side search results. Structurally, it's an array element within the response consisting of text, a document key, and a confidence score.

<a name="query-params"></a>

## How to request semantic answers in a query

To return a semantic answer, the query must have the semantic "queryType", "queryLanguage", "searchFields", and the "answers" parameter. Specifying the "answers" parameter does not guarantee that you will get an answer, but the request must include this parameter if answer processing is to be invoked at all.

The "searchFields" parameter is crucial to returning a high quality answer, both in terms of content and order (see below). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ A query string must not be null and should be formulated as question. In this preview, the "queryType" and "queryLanguage" must be set exactly as shown in the example.

+ The "searchFields" parameter determines which string fields provide tokens to the extraction model. The same fields that produce captions also produce answers. For precise guidance on how to set this field so that it works for both captions and answers, see [Set searchFields](semantic-how-to-query-request.md#searchfields). 

+ For "answers", parameter construction is `"answers": "extractive"`, where the default number of answers returned is one. You can increase the number of answers by adding a count as shown in the above example, up to a maximum of five.  Whether you need more than one answer depends on the user experience of your app, and how you want to render results.

## Deconstruct an answer from the response

Answers are provided in the @search.answers array, which appears first in the response. If an answer is indeterminate, the response will show up as `"@search.answers": []`. When designing a search results page that includes answers, be sure to handle cases where answers are not found.

Within @search.answers, the "key" is the document key or ID of the match. Given a document key, you can use [Lookup Document](/rest/api/searchservice/lookup-document) API to retrieve any or all parts of the search document to include on the search page or a detail page.

Both "text" and "highlights" provide identical content, in both plain text and with highlights. By default, highlights are styled as `<em>`, which you can override using the existing highlightPreTag and highlightPostTag parameters. As noted elsewhere, the substance of an answer is verbatim content from a search document. The extraction model looks for characteristics of an answer to find the appropriate content, but does not compose new language in the response.

The "score" is a confidence score that reflects the strength of the answer. If there are multiple answers in the response, this score is used to determine the order. Top answers and top captions can be derived from different search documents, where the top answer originates from one document, and the top caption from another, but in general you will see the same documents in the top positions within each array.

Answers are followed by the "value" array, which always includes scores, captions, and any fields that are retrievable by default. If you specified the select parameter, the "value" array is limited to the fields that you specified. For more information about items in the response, see [Create a semantic query](semantic-how-to-query-request.md).

Given the query "how do clouds form", the following answer is returned in the response:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## Tips for producing high-quality answers

For best results, return semantic answers on a document corpus having the following characteristics:

+ "searchFields" must provide fields that offer sufficient text in which an answer is likely to be found. Only verbatim text from a document can appear as an answer.

+ query strings must not be null (search=`*`) and the string should have the characteristics of a question, as opposed to a keyword search (a sequential list of arbitrary terms or phrases). If the query string does not appear to be answer, answer processing is skipped, even if the request specifies "answers" as a query parameter.

+ Semantic extraction and summarization have limits over how many tokens per document can be analyzed in a timely fashion. In practical terms, if you have large documents that run into hundreds of pages, you should try to break the content up into smaller documents first.

## Next steps

+ [Semantic search overview](semantic-search-overview.md)
+ [Semantic ranking algorithm](semantic-ranking.md)
+ [Similarity ranking algorithm](index-ranking-similarity.md)
+ [Create a semantic query](semantic-how-to-query-request.md)