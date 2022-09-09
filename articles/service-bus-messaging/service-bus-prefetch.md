---
title: Azure Service Bus prefetch messages | Microsoft Docs
description: Improve performance by prefetching Azure Service Bus messages. Messages are readily available for local retrieval before the application requests for them.
ms.topic: article
ms.date: 04/23/2021
---

# Prefetch Azure Service Bus messages
When you enable the *Prefetch* feature for any of the official Service Bus clients, the receiver acquires more messages than what the application initially asked for, up to the specified prefetch count. Please note that the JavaScript and TypeScript client does not support this feature yet.

As messages are returned to the application, the client acquires further messages in the background, to fill the prefetch buffer.

## Enabling Prefetch
To enable the Prefetch feature, set the prefetch count of the queue or subscription client to a number greater than zero. Setting the value to zero turns off prefetch. 

# [.NET](#tab/dotnet)
If you are using the latest Azure.Messaging.ServiceBus library, you can set the prefetch count property on the [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) and [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) objects. 

If you are using the older .NET client library for Service Bus (Microsoft.Azure.ServiceBus), you can set the prefetch count property on the [MessageReceiver](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount), [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) or the [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount).
 
# [Java](#tab/java)
If you are using the latest azure-messaging-servicebus library, you can set the prefetch count property on the [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusReceiver_PrefetchCount) and [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount#Azure_Messaging_ServiceBus_ServiceBusProcessor_PrefetchCount) objects. 

If you are using the older Java client library for Service Bus (azure-servicebus), you can set the prefetch count property on the [MessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.setprefetchcount#com_microsoft_azure_servicebus_IMessageReceiver_setPrefetchCount_int_), [QueueClient](/java/api/com.microsoft.azure.servicebus.queueclient.setprefetchcount#com_microsoft_azure_servicebus_QueueClient_setPrefetchCount_int_) or the [SubscriptionClient](/java/api/com.microsoft.azure.servicebus.subscriptionclient.setprefetchcount#com_microsoft_azure_servicebus_SubscriptionClient_setPrefetchCount_int_).
 
# [Python](#tab/python)

You can set **prefetch_count** on the [azure.servicebus.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.servicebusreceiver) or [azure.servicebus.aio.ServiceBusReceiver](/python/api/azure-servicebus/azure.servicebus.aio.servicebusreceiver).

---

> [!NOTE]
> Java Script SDK doesn't support the **Prefetch** feature. 

While messages are available in the prefetch buffer, any subsequent receive calls are immediately fulfilled from the buffer. The buffer is replenished in the background as space becomes available. If there are no messages available for delivery, the receive operation empties the buffer and then waits or blocks, as expected.

## Why is Prefetch not the default option?
Prefetch speeds up the message flow by having a message readily available for local retrieval before the application asks for one. This throughput gain is the result of a trade-off that the application author must make explicitly:

With the [receive-and-delete](message-transfers-locks-settlement.md#receiveanddelete) mode, all messages that are acquired into the prefetch buffer are no longer available in the queue. The messages stay only in the in-memory prefetch buffer until they're received into the application. If the application ends before the messages are received into the application, those messages are irrecoverable (lost).

In the [peek-lock](message-transfers-locks-settlement.md#peeklock) receive mode, messages fetched into the prefetch buffer are acquired into the buffer in a locked state. They have the timeout clock for the lock ticking. If the prefetch buffer is large, and processing takes so long that message locks expire while staying in the prefetch buffer or even while the application is processing the message, there might be some confusing events for the application to handle.

The application might acquire a message with an expired or imminently expiring lock. If so, the application might process the message, but then find that it can't complete the message because of a lock expiration. The application can check the `LockedUntilUtc` property (which is subject to clock skew between the broker and local machine clock). If the message lock has expired, the application must ignore the message and shouldn't make any API call on the message. If the message isn't expired but expiration is imminent, the lock can be renewed and extended by another default lock period.

If the lock silently expires in the prefetch buffer, the message is treated as abandoned and is again made available for retrieval from the queue. It might cause the message to be fetched into the prefetch buffer and placed at the end. If the prefetch buffer can't usually be worked through during the message expiration, messages are repeatedly prefetched but never effectively delivered in a usable (validly locked) state, and are eventually moved to the dead-letter queue once the maximum delivery count is exceeded.

If you need a high degree of reliability for message processing, and processing takes significant work and time, we recommend that you use the Prefetch feature conservatively, or not at all.

If you need high throughput and message processing is commonly cheap, prefetch yields significant throughput benefits.

The maximum prefetch count and the lock duration configured on the queue or subscription need to be balanced such that the lock timeout at least exceeds the cumulative expected message processing time for the maximum size of the prefetch buffer, plus one message. At the same time, the lock timeout shouldn't be so long that messages can exceed their maximum time to live when they're accidentally dropped, and so requiring their lock to expire before being redelivered.

## Next steps

Try the samples in the language of your choice to explore Azure Service Bus features. 

- [Azure Service Bus client library samples for .NET (latest)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [Azure Service Bus client library samples for Java (latest)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus client library samples for Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus client library samples for JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus client library samples for TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

Find samples for the older .NET and Java client libraries below:
- [Azure Service Bus client library samples for .NET (legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) - See the **Prefetch** sample. 
- [Azure Service Bus client library samples for Java (legacy)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus) - See the **Prefetch** sample. 
