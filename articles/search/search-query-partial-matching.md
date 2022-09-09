---
title: Partial terms, patterns, and special characters
titleSuffix: Azure Cognitive Search
description: Use wildcard, regex, and prefix queries to match on whole or partial terms in an Azure Cognitive Search query request. Hard-to-match patterns that include special characters can be resolved using full query syntax and custom analyzers.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
---
# Partial term search and patterns with special characters (wildcard, regex, patterns)

A *partial term search* refers to queries consisting of term fragments, where instead of a whole term, you might have just the start, middle, or end of term (sometimes referred to as prefix, infix, or suffix queries). A partial term search might include a combination of fragments, often with special characters such as dashes or slashes that are part of the query string. Common use-cases include parts of a phone number, URL, codes, or hyphenated compound words.

Partial term search and query strings that include special characters can be problematic if the index doesn't have tokens in the expected format. During the [lexical analysis phase](search-lucene-query-architecture.md#stage-2-lexical-analysis) of indexing (assuming the default standard analyzer), special characters are discarded, compound words are split up, and whitespace is deleted; all of which can cause queries to fail when no match is found. For example, a phone number like `+1 (425) 703-6214` (tokenized as `"1"`, `"425"`, `"703"`, `"6214"`) won't show up in a `"3-62"` query because that content doesn't actually exist in the index. 

The solution is to invoke an analyzer during indexing that preserves a complete string, including spaces and special characters if necessary, so that you can include the spaces and characters in your query string. Likewise, having a complete string that is not tokenized into smaller parts enables pattern matching for "starts with" or "ends with" queries, where the pattern you provide can be evaluated against a term that is not transformed by lexical analysis. Creating an additional field for an intact string, plus using a content-preserving analyzer that emits whole-term tokens, is the solution for both pattern matching and for matching on query strings that include special characters.

> [!TIP]
> If you are familiar with Postman and REST APIs, [download the query examples collection](https://github.com/Azure-Samples/azure-search-postman-samples/) to query partial terms and special characters described in this article.

## What is partial term search in Azure Cognitive Search

Azure Cognitive Search scans for whole tokenized terms in the index and won't find a match on a partial term unless you include wildcard placeholder operators (`*` and `?`) , or format the query as a regular expression. Partial terms are specified using these techniques:

+ [Regular expression queries](query-lucene-syntax.md#bkmk_regex) can be any regular expression that is valid under Apache Lucene. 

+ [Wildcard operators with prefix matching](query-simple-syntax.md#prefix-search) refers to a generally recognized pattern that includes the beginning of a term, followed by `*` or `?` suffix operators, such as `search=cap*` matching on "Cap'n Jack's Waterfront Inn" or "Gacc Capital". Prefixing matching is supported in both simple and full Lucene query syntax.

+ [Wildcard with infix and suffix matching](query-lucene-syntax.md#bkmk_wildcard) places the `*` and `?` operators inside or at the beginning of a term, and requires regular expression syntax (where the expression is enclosed with forward slashes). For example, the query string (`search=/.*numeric*./`) returns results on "alphanumeric" and "alphanumerical" as suffix and infix matches.

For partial term or pattern search, and a few other query forms like fuzzy search, analyzers are not used at query time. For these query forms, which the parser detects by the presence of operators and delimiters, the query string is passed to the engine without lexical analysis. For these query forms, the analyzer specified on the field is ignored.

> [!NOTE]
> When a partial query string includes characters, such as slashes in a URL fragment, you might need to add escape characters. In JSON, a forward slash `/` is escaped with a backward slash `\`. As such, `search=/.*microsoft.com\/azure\/.*/` is the syntax for the URL fragment "microsoft.com/azure/".

## Solving partial/pattern search problems

When you need to search on fragments or patterns or special characters, you can override the default analyzer with a custom analyzer that operates under simpler tokenization rules, retaining the entire string in the index. Taking a step back, the approach looks like this:

+ Define a field to store an intact version of the string (assuming you want analyzed and non-analyzed text at query time)
+ Evaluate and choose among the various analyzers that emit tokens at the right level of granularity
+ Assign the analyzer to the field
+ Build and test the index

> [!TIP]
> Evaluating analyzers is an iterative process that requires frequent index rebuilds. You can make this step easier by using Postman, the REST APIs for [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index), [Delete Index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents), and [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). For Load Documents, the request body should contain a small representative data set that you want to test (for example, a field with phone numbers or product codes). With these APIs in the same Postman collection, you can cycle through these steps quickly.

## Duplicate fields for different scenarios

Analyzers determine how terms are tokenized in an index. Since analyzers are assigned on a per-field basis, you can create fields in your index to optimize for different scenarios. For example, you might define "featureCode" and "featureCodeRegex" to support regular full text search on the first, and advanced pattern matching on the second. The analyzers assigned to each field determine how the contents of each field are tokenized in the index.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## Choose an analyzer

When choosing an analyzer that produces whole-term tokens, the following analyzers are common choices:

| Analyzer | Behaviors |
|----------|-----------|
| [language analyzers](index-add-language-analyzers.md) | Preserves hyphens in compound words or strings, vowel mutations, and verb forms. If query patterns include dashes, using a language analyzer might be sufficient. |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Content of the entire field is tokenized as a single term. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separates on white spaces only. Terms that include dashes or other characters are treated as a single token. |
| [custom analyzer](index-add-custom-analyzers.md) | (recommended) Creating a custom analyzer lets you specify both the tokenizer and token filter. The previous analyzers must be used as-is. A custom analyzer lets you pick which tokenizers and token filters to use. <br><br>A recommended combination is the [keyword tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) with a [lower-case token filter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). By itself, the predefined [keyword analyzer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) does not lower-case any upper-case text, which can cause queries to fail. A custom analyzer gives you a mechanism for adding the lower-case token filter. |

If you are using a web API test tool like Postman, you can add the [Test Analyzer REST call](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) to inspect tokenized output.

You must have a populated index to work with. Given an existing index and a field containing dashes or partial terms, you can try various analyzers over specific terms to see what tokens are emitted.  

1. First, check the Standard analyzer to see how terms are tokenized by default.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Evaluate the response to see how the text is tokenized within the index. Notice how each term is lower-cased and broken up. Only those queries that match on these tokens will return this document in the results. A query that includes "10-NOR" will fail.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Now modify the request to use the `whitespace` or `keyword` analyzer:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Now the response consists of a single token, upper-cased, with dashes preserved as a part of the string. If you need to search on a pattern or a partial term such as "10-NOR", the query engine now has the basis for finding a match.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Be aware that query parsers often lower-case terms in a search expression when building the query tree. If you are using an analyzer that does not lower-case text inputs during indexing, and you are not getting expected results, this could be the reason. The solution is to add a lower-case token filter, as described in the "Use custom analyzers" section below.

## Configure an analyzer
 
Whether you are evaluating analyzers or moving forward with a specific configuration, you will need to specify the analyzer on the field definition, and possibly configure the analyzer itself if you are not using a built-in analyzer. When swapping analyzers, you typically need to rebuild the index (drop, recreate, and reload). 

### Use built-in analyzers

Built-in or predefined analyzers can be specified by name on an `analyzer` property of a field definition, with no additional configuration required in the index. The following example demonstrates how you would set the `whitespace` analyzer on a field. 

For other scenarios and to learn more about other built-in analyzers, see [Predefined analyzers list](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### Use custom analyzers

If you are using a [custom analyzer](index-add-custom-analyzers.md), define it in the index with a user-defined combination of tokenizer, token filter, with possible configuration settings. Next, reference it on a field definition, just as you would a built-in analyzer.

When the objective is whole-term tokenization, a custom analyzer that consists of a **keyword tokenizer** and **lower-case token filter** is recommended.

+ The keyword tokenizer creates a single token for the entire contents of a field.
+ The lowercase token filter transforms upper-case letters into lower-case text. Query parsers typically lowercase any uppercase text inputs. Lower-casing homogenizes the inputs with the tokenized terms.

The following example illustrates a custom analyzer that provides the keyword tokenizer and a lowercase token filter.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> The `keyword_v2` tokenizer and `lowercase` token filter are known to the system and using their default configurations, which is why you can reference them by name without having to define them first.

## Build and test

Once you have defined an index with analyzers and field definitions that support your scenario, load documents that have representative strings so that you can test partial string queries. 

The previous sections explained the logic. This section steps through each API you should call when testing your solution. As previously noted, if you use an interactive web test tool such as Postman, you can step through these tasks quickly.

+ [Delete Index](https://docs.microsoft.com/rest/api/searchservice/delete-index) removes an existing index of the same name so that you can recreate it.

+ [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) creates the index structure on your search service, including analyzer definitions and fields with an analyzer specification.

+ [Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) imports documents having the same structure as your index, as well as searchable content. After this step, your index is ready to query or test.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) was introduced in [Choose an analyzer](#choose-an-analyzer). Test some of the strings in your index using a variety of analyzers to understand how terms are tokenized.

+ [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) explains how to construct a query request, using either [simple syntax](query-simple-syntax.md) or [full Lucene syntax](query-lucene-syntax.md) for wildcard and regular expressions.

  For partial term queries, such as querying "3-6214" to find a match on "+1 (425) 703-6214", you can use the simple syntax: `search=3-6214&queryType=simple`.

  For infix and suffix queries, such as querying "num" or "numeric to find a match on "alphanumeric", use the full Lucene syntax and a regular expression: `search=/.*num.*/&queryType=full`

## Tune query performance

If you implement the recommended configuration that includes the keyword_v2 tokenizer and lower-case token filter, you might notice a decrease in query performance due to the additional token filter processing over existing tokens in your index. 

The following example adds an [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) to make prefix matches faster. Additional tokens are generated for in 2-25 character combinations that include characters: (not only MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

As you can imagine, the additional tokenization results in a larger index. If you have sufficient capacity to accommodate the larger index, this approach with its faster response time might be a better solution.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## Next steps

This article explains how analyzers both contribute to query problems and solve query problems. As a next step, take a closer look at analyzer impact on indexing and query processing. In particular, consider using the Analyze Text API to return tokenized output so that you can see exactly what an analyzer is creating for your index.

+ [Language analyzers](search-language-support.md)
+ [Analyzers for text processing in Azure Cognitive Search](search-analyzers.md)
+ [Analyze Text API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [How full text search works (query architecture)](search-lucene-query-architecture.md)
