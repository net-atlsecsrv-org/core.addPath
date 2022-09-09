---
title: Understanding resource sets
description: This article explains what resource sets are and how Azure Purview creates them.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
---

# Understanding resource sets

This article helps you understand how Azure Purview uses resource sets to map data assets to logical resources.

## Background info

At-scale data processing systems typically store a single table on a disk as multiple files. This concept is represented in Azure Purview by using resource sets. A resource set is a single object in the catalog that represents a large number of assets in storage.

For example, suppose your Spark cluster has persisted a DataFrame into an ADLS Gen2 data source. Although in Spark the table looks like a single logical resource, on the disk there are likely thousands of Parquet files, each of which represents a partition of the total DataFrame's contents. IoT data and web log data have the same challenge. Imagine you have a sensor that outputs log files several times a second. It won't take long until you have hundreds of thousands of log files from that single sensor.

To address the challenge of mapping large numbers of data assets to a single logical resource, Azure Purview uses resource sets.

## How Azure Purview detects resource sets

Azure Purview supports detecting resource sets only in Azure Blobs, ADLS Gen1, and ADLS Gen2.

Azure Purview automatically detects resource sets by using a feature called automated resource set discovery. This feature looks at all of the data that's ingested via scanning and compares it to a set of defined patterns.

For example, suppose you scan a data source whose URL is `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet`. Azure Purview looks at the path segments and determines if they match any built-in patterns. It has built-in patterns for GUIDs, numbers, date formats, localization codes (for example, en-us), and so on. In this case, the number pattern matches *23*. Azure Purview assumes that this file is part of a resource set named `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet`.

Or, for a URL like `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json`, Azure Purview matches both the localization pattern and the number pattern, producing a resource set named `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`.

Using this strategy, Azure Purview would map the following resources to the same resource set, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Azure Data Lake Storage Gen2 is now generally available. We recommend that you start using it today. For more information, see the [product page](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## File types that Azure Purview will not detect as resource sets

Purview intentionally does not try to classify most Document File Types like Word, Excel or PDF as Resource Sets. The exception are CSVs since that is a common partitioned file format.

## How Azure Purview scans resource sets

When Azure Purview detects resources that it thinks are part of a resource set, it switches from a full scan to a sample scan. In a sample scan, it opens only a subset of the files that it thinks are in the resource set. For each file it does open, it uses its schema and runs its classifiers. Azure Purview then finds the newest resource among the opened resources and uses that resource's schema and classifications in the entry for the entire resource set in the catalog.

## What Azure Purview stores about resource sets

In addition to single schema and classifications, Azure Purview stores the following information about resource sets:

- Data from the latest partition resource it deep scanned.
- Aggregate information about the partition resources that make up the resource set.
- A partition count that shows how many partition resources it found.
- A schema count that shows how many unique schemas it found in the sample set it did deep scans on. This value is either a number between 1–5, or for values greater than 5, 5+.
- A list of partition types when more than a single partition type is included in the resource set. For example, an IoT sensor might output both XML and JSON files, although both are logically part of the same resource set.

## Built-in resource set patterns

Azure Purview supports the following resource set patterns. These patterns can appear as a name in a directory or as part of a file name.

| Pattern name | Display name | Description |
|--------------|--------------|-------------|
| GUID         | {GUID}       | A globally unique identifier, as defined in [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Number       | {N}          | One or more digits. |
| Date/time formats | {N}     | Azure Purview supports different kinds of date/time formats, but all are reduced to a series of {N}s. |
| 4ByteHex     | {HEX}        | A four-digit hexadecimal number. |
| Localization | {LOC}        | A language tag, as defined in [BCP 47](https://tools.ietf.org/html/bcp47). Azure Purview supports tags that contain either a hyphen (-) or an underscore (_). For example, en_ca and en-ca. |

## Issues with resource sets

Although resource sets work well in most cases, you might encounter the  following issues, in which Azure Purview:

- Incorrectly marks an asset as a resource set
- Puts an asset into the wrong resource set
- Incorrectly marks an asset as not being a resource set

## Next steps

To get started with Data Catalog, see [Quickstart: Create an Azure Purview account](create-catalog-portal.md).
