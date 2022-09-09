---
title: Use Azure Event Grid with events in CloudEvents schema
description: Describes how to use the CloudEvents schema for events in Azure Event Grid. The service supports events in the JSON implementation of Cloud Events. 
services: event-grid
author: banisadr

ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
---

# CloudEvents v1.0 schema with Event Grid

In addition to its [default event schema](event-schema.md), Azure Event Grid natively supports events in the [JSON implementation of CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) and [HTTP protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) is an [open specification](https://github.com/cloudevents/spec/blob/v1.0/spec.md) for describing event data.

CloudEvents simplifies interoperability by providing a common event schema for publishing, and consuming cloud based events. This schema allows for uniform tooling, standard ways of routing & handling events, and universal ways of deserializing the outer event schema. With a common schema, you can more easily integrate work across platforms.

CloudEvents is being built by several [collaborators](https://github.com/cloudevents/spec/blob/master/community/contributors.md), including Microsoft, through the [Cloud Native Computing Foundation](https://www.cncf.io/). It's currently available as version 1.0.

This article describes CloudEvents schema with Event Grid.

## Sample event using CloudEvents schema

Here is an example of an Azure Blob Storage event in CloudEvents format:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

A detailed description of the available fields, their types, and definitions in CloudEvents v1.0 is [available here](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

The headers values for events delivered in the CloudEvents schema and the Event Grid schema are the same except for `content-type`. For CloudEvents schema, that header value is `"content-type":"application/cloudevents+json; charset=utf-8"`. For Event Grid schema, that header value is `"content-type":"application/json; charset=utf-8"`.

## Event Grid for CloudEvents

You can use Event Grid for both input and output of events in CloudEvents schema. You can use CloudEvents for system events, like Blob Storage events and IoT Hub events, and custom events. It can also transform those events on the wire back and forth.


| Input schema       | Output schema
|--------------------|---------------------
| CloudEvents format | CloudEvents format
| Event Grid format  | CloudEvents format
| Event Grid format  | Event Grid format

For all event schemas, Event Grid requires validation when publishing to an event grid topic and when creating an event subscription. For more information, see [Event Grid security and authentication](security-authentication.md).

## Next steps
See [How to use CloudEvents v1.0 schema with Event Grid](cloudevents-schema.md).  
