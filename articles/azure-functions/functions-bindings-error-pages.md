---
title: Azure Functions error handling and retry guidance
description: Learn to handle errors and retry events in Azure Functions with links to specific binding errors.
author: craigshoemaker

ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
---

# Azure Functions error handling and retries

Handling errors in Azure Functions is important to avoid lost data, missed events, and to monitor the health of your application.

This article describes general strategies for error handling along with links to binding-specific errors.

## Handling errors

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## Binding error codes

When integrating with Azure services, errors may originate from the APIs of the underlying services. Information relating to binding-specific errors is available in the **Exceptions and return codes** section of the following articles:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hubs](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
