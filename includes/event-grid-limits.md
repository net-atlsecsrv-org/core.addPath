---
 title: include file
 description: include file
 services: event-grid
 author: tfitzmac
 ms.service: event-grid
 ms.topic: include
 ms.date: 05/22/2019
 ms.author: tomfitz
 ms.custom: include file
---

The following limits apply to Azure Event Grid system topics and custom topics, *not* event domains.

| Resource | Limit |
| --- | --- |
| Custom topics per Azure subscription | 100 |
| Event subscriptions per topic | 500 |
| Publish rate for a custom topic (ingress) | 5,000 events per second per topic |
| Publish requests | 250 per second |
| Event size | 1 MB. Operations are charged in 64 KB increments though. So, events over 64 KB will incur operations charges as though they were multiple events. For example, an event that is 130 KB would incur operations as though it were 3 separate events.  |

The following limits apply to event domains only.

| Resource | Limit |
| --- | --- |
| Topics per event domain | 100,000 |
| Event subscriptions per topic within a domain | 500 |
| Domain scope event subscriptions | 50 |
| Publish rate for an event domain (ingress) | 5,000 events per second |
| Publish requests | 250 per second |
| Event Domains per Azure Subscription | 100 |