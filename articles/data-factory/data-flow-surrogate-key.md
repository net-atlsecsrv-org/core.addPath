---
title: Surrogate key transformation in mapping data flow 
description: How to use Azure Data Factory's mapping data flow Surrogate Key Transformation to generate sequential key values
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
---

# Surrogate key transformation in mapping data flow 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use the surrogate key transformation to add an incrementing key value to each row of data. This is useful when designing dimension tables in a star schema analytical data model. In a star schema, each member in your dimension tables requires a unique key that is a non-business key.

## Configuration

![Surrogate Key Transform](media/data-flow/surrogate.png "Surrogate Key Transformation")

**Key column:** The name of the generated surrogate key column.

**Start value:** The lowest key value that will be generated.

## Increment keys from existing sources

To start your sequence from a value that exists in a source, we recommend to use a cache sink to save that value and use a derived column transformation to add the two values together. Use a cached lookup to get the output and append it to the generated key. For more information, learn about [cache sinks](data-flow-sink.md#cache-sink) and [cached lookups](concepts-data-flow-expression-builder.md#cached-lookup).

![Surrogate Key lookup](media/data-flow/cached-lookup-example.png "Surrogate Key lookup")

### Increment from existing maximum value

To seed the key value with the previous max, there are two techniques that you can use based on where your source data is.

#### Database sources

Use a SQL query option to select MAX() from your source. For example, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Surrogate Key Query](media/data-flow/surrogate-key-max-database.png "Surrogate Key Transformation Query")

#### File sources

If your previous max value is in a file, use the `max()` function in the aggregate transformation to get the previous max value:

![Surrogate Key File](media/data-flow/surrogate-key-max-file.png "Surrogate Key File")

In both cases, you will need to write to a cache sink and lookup the value. 


## Data flow script

### Syntax

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### Example

![Surrogate Key Transform](media/data-flow/surrogate.png "Surrogate Key Transformation")

The data flow script for the above surrogate key configuration is in the code snippet below.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## Next steps

These examples use the [Join](data-flow-join.md) and [Derived Column](data-flow-derived-column.md) transformations.
