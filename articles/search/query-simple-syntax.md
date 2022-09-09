---
title: Simple query syntax
titleSuffix: Azure Cognitive Search
description: Reference for the simple query syntax used for full text search queries in Azure Cognitive Search.

manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
---

# Simple query syntax in Azure Cognitive Search

Azure Cognitive Search implements two Lucene-based query languages: [Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) and the [Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). The simple parser is more flexible and will attempt to interpret a request even if it's not perfectly composed. Because of this flexibility, it is the default for queries in Azure Cognitive Search.

The simple syntax is used for query expressions passed in the **`search`** parameter of a [Search Documents (REST API)](/rest/api/searchservice/search-documents) request, not to be confused with the [OData syntax](query-odata-filter-orderby-syntax.md) used for the [**`$filter`**](search-filters.md) and [**`$orderby`**](search-query-odata-orderby.md) expressions in the same request. OData parameters have different syntax and rules for constructing queries, escaping strings, and so on.

Although the simple parser is based on the [Apache Lucene Simple Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) class, the implementation in Cognitive Search excludes fuzzy search. If you need [fuzzy search](search-query-fuzzy.md), consider the alternative [full Lucene query syntax](query-lucene-syntax.md) instead.

## Example (simple syntax)

Although **`queryType`** is set below, it's the default and can be omitted unless you are reverting from an alternative type. The following example is a search over independent terms, with a requirement that all matching documents include "pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

The **`searchMode`** parameter is relevant in this example. Whenever boolean operators are on the query, you should generally set `searchMode=all` to ensure that *all* of the criteria is matched. Otherwise, you can use the default `searchMode=any` that favors recall over precision.

For additional examples, see [Simple query syntax examples](search-query-simple-examples.md). For details about the query request and parameters, see [Search Documents (REST API)](/rest/api/searchservice/Search-Documents).

## Keyword search on terms and phrases

Strings passed to the **`search`** parameter can include terms or phrases in any supported language, boolean operators, precedence operators, wildcard or prefix characters for "starts with" queries, escape characters, and URL encoding characters. The **`search`** parameter is optional. Unspecified, search (`search=*` or `search=" "`) returns the top 50 documents in arbitrary (unranked) order.

+ A *term search* is a query of one or more terms, where any of the terms are considered a match.

+ A *phrase search* is an exact phrase enclosed in quotation marks `" "`. For example, while ```Roach Motel``` (without quotes) would search for documents containing ```Roach``` and/or ```Motel``` anywhere in any order, ```"Roach Motel"``` (with quotes) will only match documents that contain that whole phrase together and in that order (lexical analysis still applies). 

  Depending on your search client, you might need to escape the quotation marks in a phrase search. For example, in Postman in a POST request, a phrase search on `"Roach Motel"` in the request body would be specified as `"\"Roach Motel\""`.

By default, all terms or phrases passed in the **`search`** parameter undergo lexical analysis. Make sure you understand the tokenization behavior of the analyzer you are using. Often, when query results are unexpected, the reason can be traced to how terms are tokenized at query time.

Any text with one or more terms is considered a valid starting point for query execution. Azure Cognitive Search will match documents containing any or all of the terms, including any variations found during analysis of the text.

As straightforward as this sounds, there is one aspect of query execution in Azure Cognitive Search that *might* produce unexpected results, increasing rather than decreasing search results as more terms and operators are added to the input string. Whether this expansion actually occurs depends on the inclusion of a NOT operator, combined with a **`searchMode`** parameter setting that determines how NOT is interpreted in terms of AND or OR behaviors. For more information, see the NOT operator under [Boolean operators](#boolean-operators).

## Boolean operators

You can embed Boolean operators in a query string to improve the precision of a match. In the simple syntax, boolean operators are character-based. Text operators, such as the word AND, are not supported.

| Character | Example | Usage |
|----------- |--------|-------|
| `+` | `pool + ocean` | An AND operation. For example, `pool + ocean` stipulates that a document must contain both terms.|
| `|` | `pool | ocean` | An OR operation finds a match when either term is found. In the example, the query engine will return match on documents containing either `pool` or `ocean` or both. Because OR is the default conjunction operator, you could also leave it out, such that `pool ocean` is the equivalent of  `pool | ocean`.|
| `-` | `pool – ocean` | A NOT operation returns matches on documents that exclude the term. <br/><br/>To get the expected behavior on a NOT expression, consider setting **`searchMode=all`** on the request. Otherwise, under the default of **`searchMode=any`**, you will get matches on `pool`, plus matches on all documents that do not contain `ocean`, which could be a lot of documents. The **`searchMode`** parameter on a query request controls whether a term with the NOT operator is ANDed or ORed with other terms in the query (assuming there is no `+` or `|` operator on the other terms). Using **`searchMode=all`** increases the precision of queries by including fewer results, and by default - will be interpreted as "AND NOT". <br/><br/>When deciding on a **`searchMode`** setting, consider the user interaction patterns for queries in various applications. Users who are searching for information are more likely to include an operator in a query, as opposed to e-commerce sites that have more built-in navigation structures. |

<a name="prefix-search"></a>

## Prefix queries

For "starts with" queries, add a suffix operator (`*`) as the placeholder for the remainder of a term. A prefix query must begin with at least one alphanumeric character before you can add the suffix operator.

| Character | Example | Usage |
|----------- |--------|-------|
| `*` | `lingui*` will match on "linguistic" or "linguini" | The asterisk (`*`) represents one or more characters of arbitrary length, ignoring case.  |

Similar to filters, a prefix query looks for an exact match. As such, there is no relevance scoring (all results receive a search score of 1.0). Be aware that prefix queries can be slow, especially if the index is large and the prefix consists of a small number of characters. An alternative methodology, such as edge n-gram tokenization, might perform faster.

Simple syntax supports prefix matching only. For suffix or infix matching against the end or middle of a term, use the [full Lucene syntax for wildcard search](query-lucene-syntax.md#bkmk_wildcard).

## Escaping search operators  

In the simple syntax, search operators include these characters: `+ | " ( ) ' \`  

If any of these characters are part of a token in the index, escape it by prefixing it with a single backslash (`\`) in the query. For example, suppose you used a custom analyzer for whole term tokenization, and your index contains the string "Luxury+Hotel". To get an exact match on this token, insert an escape character: `search=luxury\+hotel`.

To make things simple for the more typical cases, there are two exceptions to this rule where escaping is not needed:  

+ The NOT operator `-` only needs to be escaped if it's the first character after a whitespace. If the `-` appears in the middle (for example, in `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`), you can skip escaping.

+ The suffix operator `*` only needs to be escaped if it's the last character before a whitespace. If the `*` appears in the middle (for example, in `4*4=16`), no escaping is needed.

> [!NOTE]  
> By default, the standard analyzer will delete and break words on hyphens, whitespace, ampersands, and other characters during [lexical analysis](search-lucene-query-architecture.md#stage-2-lexical-analysis). If you require special characters to remain in the query string, you might need an analyzer that preserves them in the index. Some choices include Microsoft natural [language analyzers](index-add-language-analyzers.md), which preserves hyphenated words, or a custom analyzer for more complex patterns. For more information, see [Partial terms, patterns, and special characters](search-query-partial-matching.md).

## Encoding unsafe and reserved characters in URLs

Ensure all unsafe and reserved characters are encoded in a URL. For example, '#' is an unsafe character because it is a fragment/anchor identifier in a URL. The character must be encoded to `%23` if used in a URL. '&' and '=' are examples of reserved characters as they delimit parameters and specify values in Azure Cognitive Search. For more information, see [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Unsafe characters are ``" ` < > # % { } | \ ^ ~ [ ]``. Reserved characters are `; / ? : @ = + &`.

## Special characters

In some circumstances, you may want to search for a special character, like an '❤' emoji or the '€' sign. In such cases, make sure that the analyzer you use does not filter those characters out. The standard analyzer bypasses many special characters, excluding them from your index.

Analyzers that will tokenize special characters include the "whitespace" analyzer, which takes into consideration any character sequences separated by whitespaces as tokens (so the "❤" string would be considered a token). Also, a language analyzer like the Microsoft English analyzer ("en.microsoft"), would take the "€" string as a token. You can [test an analyzer](/rest/api/searchservice/test-analyzer) to see what tokens it generates for a given query.

When using Unicode characters, make sure symbols are properly escaped in the query url (for instance for "❤" would use the escape sequence `%E2%9D%A4+`). Postman does this translation automatically.  

## Precedence (grouping)

You can use parentheses to create subqueries, including operators within the parenthetical statement. For example, `motel+(wifi|luxury)` will search for documents containing the "motel" term and either "wifi" or "luxury" (or both).

## Query size limits

If your application generates search queries programmatically, we recommend designing it in such a way that it does not generate queries of unbounded size. 

+ For GET, the length of the URL cannot exceed 8 KB.

+ For POST (and any other request), where the body of the request includes `search` and other parameters such as `filter` and `orderby`, the maximum size is 16 MB, where the maximum number of clauses in `search` (expressions separated by AND, OR, and so on) is 1024. There is also a limit of approximately 32 KB on the size of any individual term in a query. For more information, see [API request limits](search-limits-quotas-capacity.md#api-request-limits).

## Next steps

If you will be constructing queries programmatically, review [Full text search in Azure Cognitive Search](search-lucene-query-architecture.md) to understand the stages of query processing and the implications of text analysis.

You can also review the following articles to learn more about query construction:

+ [Query examples for simple search](search-query-simple-examples.md)
+ [Query examples for full Lucene search](search-query-lucene-examples.md)
+ [Search Documents REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene query syntax](query-lucene-syntax.md)
+ [Filter and Select (OData) expression syntax](query-odata-filter-orderby-syntax.md)