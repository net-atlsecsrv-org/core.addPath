---
title: 'JSON flattening and escaping rules - Azure Time Series Insights Gen2 | Microsoft Docs'
description: Learn about JSON flattening, escaping, and array handling in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
---

# Ingestion Rules
### JSON Flattening, Escaping, and Array Handling

Your Azure Time Series Insights Gen2 environment will dynamically create the columns of your warm and cold stores, following a particular set of naming conventions. When an event is ingested, a set of rules is applied to the JSON payload and property names. These include escaping certain special characters and flattening nested JSON objects. It's important to know these rules so that you understand how the shape of your JSON will influence how your events are stored and queried. See the table below for the full list of rules. Examples A & B also demonstrate how you're able to efficiently batch multiple time series in an array.

> [!IMPORTANT]
>
> * Review the rules below before selecting a [Time Series ID property](time-series-insights-update-how-to-id.md) and/or your event source [timestamp propert(ies)](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). If your TS ID or timestamp is within a nested object or has one or more of the special characters below, it's important to ensure that the property name that you provide matches the column name *after* the ingestion rules have been applied. See example [B](concepts-json-flattening-escaping-rules.md#example-b) below.

| Rule | Example JSON |Column name in storage |
|---|---|---|
| The Azure Time Series Insights Gen2 data type is appended to the end of your column name as "_\<dataType\>" | ```"type": "Accumulated Heat"``` | type_string |
| The event source [timestamp property](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) will be saved in Azure Time Series Insights Gen2 as “timestamp” in storage, and the value stored in UTC. You can customize your event source(s) timestamp property to meet the needs of your solution, but the column name in warm and cold storage is "timestamp". Other datetime JSON properties that are not the event source timestamp will be saved with "_datetime" in the column name, as mentioned in the rule above.  | ```"ts": "2020-03-19 14:40:38.318"``` | timestamp |
| JSON property names that include the special characters . [  \ and ' are escaped using [' and ']  |  ```"id.wasp": "6A3090FD337DE6B"``` | ['id.wasp']_string |
| Within [' and '] there's additional escaping of single quotes and backslashes. A single quote will be written as \’ and a backslash will be written as \\\ | ```"Foo's Law Value": "17.139999389648"``` | ['Foo\'s Law Value']_double |
| Nested JSON objects are flattened with a period as the separator. Nesting up to 10 levels is supported. |  ```"series": {"value" : 316 }``` | series.value_long |
| Arrays of primitive types are stored as the Dynamic type |  ```"values": [154, 149, 147]``` | values_dynamic |
| Arrays containing objects have two behaviors depending on the object content: If either the TS ID(s) or timestamp property(ies) are within the objects in an array, the array will be unrolled such that the initial JSON payload produces multiple events. This enables you to batch multiple events into one JSON structure. Any top-level properties that are peers to the array will be saved with each unrolled object. If your TS ID(s) and timestamp are *not* within the array, it will be saved whole as the Dynamic type. | See examples [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b) and [C](concepts-json-flattening-escaping-rules.md#example-c) below
| Arrays containing mixed elements aren't flattened. |  ```"values": ["foo", {"bar" : 149}, 147]``` | values_dynamic |
| 512 characters is the JSON property name limit. If the name exceeds 512 characters, it will be truncated to 512 and '_<'hashCode'>' is appended. **Note** that this also applies to property names that have been concatenated from object flattened, denoting a nested object path. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` | data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double |

## Understanding the dual behavior for arrays

Arrays of objects will either be stored whole or split into multiple events depending on how you've modeled your data. This allows you to  use an array to batch events, and avoid repeating telemetry properties that are defined at the root object level. Batching may be advantageous as it results in fewer Event Hubs or IoT Hub messages sent. 

However, in some cases, arrays containing objects are only meaningful in the context of other values. Creating multiple events would render the data meaningless. To ensure that an array of objects is stored as-is as a dynamic type, follow the data modeling guidance below and take a look at [Example C](concepts-json-flattening-escaping-rules.md#example-c)

### How do I know if my array of objects will produce multiple events?

If one or more of your Time Series ID propert(ies) is nested within objects in an array, *or* if your event source timestamp property is nested, the ingestion engine will split it out to create multiple events. The property names that you provided for your TS ID(s) and/or timestamp should follow the flattening rules above, and will therefore indicate the shape of your JSON. See the examples below, and check out the guide on how to [select a Time Series ID property.](time-series-insights-update-how-to-id.md)

### Example A:
Time Series ID at the object root and timestamp nested<br/>
**Environment Time Series ID:** `"id"`<br/>
**Event source timestamp:** `"values.time"`<br/>
**JSON payload:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Result in Parquet file:**
<br/>
The configuration and payload above will produce three columns and four events

| timestamp  | id_string | values.value_double 
| ---- | ---- | ---- | 
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` | 
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` | 
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` | 
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` | 

### Example B:
Composite Time Series ID with one property nested<br/> 
**Environment Time Series ID:** `"plantId"` and `"telemetry.tagId"`<br/>
**Event source timestamp:** `"timestamp"`<br/>
**JSON payload:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Result in Parquet file:**
<br/>
The configuration and payload above will produce four columns and six events

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double 
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A4`` | 19.960796 | 

### Example C:
Time Series ID and timestamp are at the object root<br/> 
**Environment Time Series ID:** `"id"`<br/>
**Event source timestamp:** `"timestamp"`<br/>
**JSON payload:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
    		"value": 120
    	},
    	{
    		"value": 124
    	}
    ]
}
```

**Result in Parquet file:**
<br/>
The configuration and payload above will produce three columns and one event

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- | 
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## Next steps

* Understand your environment's [throughput limitations](./concepts-streaming-ingress-throughput-limits.md)
