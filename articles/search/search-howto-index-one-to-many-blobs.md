---
title: Index blobs containing multiple documents  
titleSuffix: Azure Cognitive Search
description: Crawl Azure blobs for text content using the Azure Cognitive Search Blob indexer, where each blob might yield one or more search index documents.

manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
---

# Indexing blobs to produce multiple search documents
By default, a blob indexer will treat the contents of a blob as a single search document. Certain **parsingMode** values support scenarios where an individual blob can result in multiple search documents. The different types of **parsingMode** that allow an indexer to extract more than one search document from a blob are:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## One-to-many document key
Each document that shows up in an Azure Cognitive Search index is uniquely identified by a document key. 

When no parsing mode is specified, and if there is no explicit mapping for the key field in the index Azure Cognitive Search automatically [maps](search-indexer-field-mappings.md) the `metadata_storage_path` property as the key. This mapping ensures that each blob appears as a distinct search document.

When using any of the parsing modes listed above, one blob maps to "many" search documents, making a document key solely based on blob metadata unsuitable. To overcome this constraint, Azure Cognitive Search is capable of generating a "one-to-many" document key for each individual entity extracted from a blob. This property is named `AzureSearch_DocumentKey` and is added to each individual entity extracted from the blob. The value of this property is guaranteed to be unique for each individual entity _across blobs_ and the entities will show up as separate search documents.

By default, when no explicit field mappings for the key index field are specified, the `AzureSearch_DocumentKey` is mapped to it, using the `base64Encode` field-mapping function.

## Example
Assume you've an index definition with the following fields:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

And your blob container has blobs with the following structure:

_Blob1.json_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.json_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

When you create an indexer and set the **parsingMode** to `jsonLines` - without specifying any explicit field mappings for the key field, the following mapping will be applied implicitly

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

This setup will result in the Azure Cognitive Search index containing the following information (base64 encoded ID shortened for brevity)

| ID | temperature | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## Custom field mapping for index key field

Assuming the same index definition as the previous example, say your blob container has blobs with the following structure:

_Blob1.json_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

When you create an indexer with `delimitedText` **parsingMode**, it might feel natural to set up a field-mapping function to the key field as follows:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

However, this mapping will _not_ result in 4 documents showing up in the index, because the `recordid` field is not unique _across blobs_. Hence, we recommend you to make use of the implicit field mapping applied from the `AzureSearch_DocumentKey` property to the key index field for "one-to-many" parsing modes.

If you do want to set up an explicit field mapping, make sure that the _sourceField_ is distinct for each individual entity **across all blobs**.

> [!NOTE]
> The approach used by `AzureSearch_DocumentKey` of ensuring uniqueness per extracted entity is subject to change and therefore you should not rely on it's value for your application's needs.

## Help us make Azure Cognitive Search better
If you have feature requests or ideas for improvements, provide your input on [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). If you need help using the existing feature, post your question on [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## Next steps

If you aren't already familiar with the basic structure and workflow of blob indexing, you should review [Indexing Azure Blob Storage with Azure Cognitive Search](search-howto-index-json-blobs.md) first. For more information about parsing modes for different blob content types, review the following articles.

> [!div class="nextstepaction"]
> [Indexing  CSV blobs](search-howto-index-csv-blobs.md)
> [Indexing JSON blobs](search-howto-index-json-blobs.md)
