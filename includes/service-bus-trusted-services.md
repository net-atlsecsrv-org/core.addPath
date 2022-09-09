---
title: include file
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: "include file"

---

## Trusted Microsoft services
When you enable the **Allow trusted Microsoft services to bypass this firewall** setting, the following services are granted access to your Service Bus resources.

| Trusted service | Supported usage scenarios | 
| --------------- | ------------------------- | 
| Azure Event Grid | Allows Azure Event Grid to send events to queues or topics in your Service Bus namespace. You also need to do the following steps: <ul><li>Enable system-assigned identity for a topic or a domain</li><li>Add the identity to the Azure Service Bus Data Sender role on the Service Bus namespace</li><li>Then, configure the event subscription that uses a Service Bus queue or topic as an endpoint to use the system-assigned identity.</li></ul> <p>For more information, see [Event delivery with a managed identity](../articles/event-grid/managed-service-identity.md)</p>|
