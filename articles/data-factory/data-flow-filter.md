---
title: Filter transformation in mapping data flow 
description: Filter out rows using the filter transformation in Azure Data Factory mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
---

# Filter transformation in mapping data flow

The Filter transforms allows row filtering based upon a condition. The output stream includes all rows that matching the filtering condition. The filter transformation is similar to a WHERE clause in SQL.

## Configuration

Use the data flow expression builder to enter an expression for the filter condition. To open the expression builder, click on the blue box. The filter condition must be of type boolean. For more information on how to create an expression, see the [expression builder](concepts-data-flow-expression-builder.md) documentation.

![Filter transformation](media/data-flow/filter1.png "Filter transformation")

## Data flow script

### Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### Example

The below example is a filter transformation named `FilterBefore1960` that takes in incoming stream `CleanData`. The filter condition is the expression `year <= 1960`.

In the Data Factory UX, this transformation looks like the below image:

![Filter transformation](media/data-flow/filter1.png "Filter transformation")

The data flow script for this transformation is in the snippet below:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## Next steps

Filter out columns with the [select transformation](data-flow-select.md)
