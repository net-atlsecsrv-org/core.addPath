---
title: Azure Service Bus message sessions | Microsoft Docs
description: Handle sequences of Azure Service Bus messages with sessions.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru

ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab

---

# Message sessions: first in, first out (FIFO) 

Microsoft Azure Service Bus sessions enable joint and ordered handling of unbounded sequences of related messages. To realize a FIFO guarantee in Service Bus, use Sessions. Service Bus is not prescriptive about the nature of the relationship between the messages, and also does not define a particular model for determining where a message sequence starts or ends.

> [!NOTE]
> The basic tier of Service Bus does not support sessions. The standard and premium tiers support sessions. For more information, see [Service Bus pricing](https://azure.microsoft.com/pricing/details/service-bus/).

Any sender can create a session when submitting messages into a topic or queue by setting the [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) property to some application-defined identifier that is unique to the session. At the AMQP 1.0 protocol level, this value maps to the *group-id* property.

On session-aware queues or subscriptions, sessions come into existence when there is at least one message with the session's [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Once a session exists, there is no defined time or API for when the session expires or disappears. Theoretically, a message can be received for a session today, the next message in a year's time, and if the **SessionId** matches, the session is the same from the Service Bus perspective.

Typically, however, an application has a clear notion of where a set of related messages starts and ends. Service Bus does not set any specific rules.

An example of how to delineate a sequence for transferring a file is to set the **Label** property for the first message to **start**, for intermediate messages to **content**, and for the last message to **end**. The relative position of the content messages can be computed as the current message *SequenceNumber* delta from the **start** message *SequenceNumber*.

The session feature in Service Bus enables a specific receive operation, in the form of [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in the C# and Java APIs. You enable the feature by setting the [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) property on the queue or subscription via Azure Resource Manager, or by setting the flag in the portal. This is required before you attempt to use the related API operations.

In the portal, set the flag with the following check box:

![][2]

The APIs for sessions exist on queue and subscription clients. There is an imperative model that controls when sessions and messages are received, and a handler-based model, similar to *OnMessage*, that hides the complexity of managing the receive loop.

## Session features

Sessions provide concurrent de-multiplexing of interleaved message streams while preserving and guaranteeing ordered delivery.

![][1]

A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) receiver is created by the client accepting a session. The client calls [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) or [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) in C#. In the reactive callback model, it registers a session handler.

When the [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) object is accepted and while it is held by a client, that client holds an exclusive lock on all messages with that session's [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) that exist in the queue or subscription, and also on all messages with that **SessionId** that still arrive while the session is held.

The lock is released when **Close** or **CloseAsync** are called, or when the lock expires in cases in which the application is unable to perform the close operation. The session lock should be treated like an exclusive lock on a file, meaning that the application should close the session as soon as it no longer needs it and/or does not expect any further messages.

When multiple concurrent receivers pull from the queue, the messages belonging to a particular session are dispatched to the specific receiver that currently holds the lock for that session. With that operation, an interleaved message stream residing in one queue or subscription is cleanly de-multiplexed to different receivers and those receivers can also live on different client machines, since the lock management happens service-side, inside Service Bus.

The previous illustration shows three concurrent session receivers. One Session with `SessionId` = 4 has no active, owning client, which means that no messages are delivered from this specific session. A session acts in many ways like a sub queue.

The session lock held by the session receiver is an umbrella for the message locks used by the *peek-lock* settlement mode. A receiver cannot have two messages concurrently "in flight," but the messages must be processed in order. A new message can only be obtained when the prior message has been completed or dead-lettered. Abandoning a message causes the same message to be served again with the next receive operation.

## Message session state

When workflows are processed in high-scale, high-availability cloud systems, the workflow handler associated with a particular session must be able to recover from unexpected failures and also be capable of resuming partially completed work on a different process or machine from where the work began.

The session state facility enables an application-defined annotation of a message session inside the broker, so that the recorded processing state relative to that session becomes instantly available when the session is acquired by a new processor.

From the Service Bus perspective, the message session state is an opaque binary object that can hold data of the size of one message, which is 256 KB for Service Bus Standard, and 1 MB for Service Bus Premium. The processing state relative to a session can be held inside the session state, or the session state can point to some storage location or database record that holds such information.

The APIs for managing session state, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) and [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), can be found on the [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) object in both the C# and Java APIs. A session that had previously no session state set returns a **null** reference for **GetState**. Clearing the previously set session state is done with [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Note that session state remains as long as it is not cleared up (returning **null**), even if all messages in a session are consumed.

All existing sessions in a queue or subscription can be enumerated with the **SessionBrowser** method in the Java API and with [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) on the [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) and [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) in the .NET client.

The session state held in a queue or in a subscription counts towards that entity's storage quota. When the application is finished with a session, it is therefore recommended for the application to clean up its retained state to avoid external management cost.

## Impact of delivery count

The definition of delivery count per message in the context of sessions varies slightly from the definition in the absense of sessions. Here is a table summarizing when the delivery count is incremented.

| Scenario | Is the message's delivery count incremented |
|----------|---------------------------------------------|
| Session is accepted, but the session lock expires (due to timeout) | Yes |
| Session is accepted, the messages within the session are not completed (even if they are locked), and the session is closed | No |
| Session is accepted, messages are completed, and then the session is explicitly closed | N/A (this is the standard flow. Here messages are removed from the session) |

## Next steps

- See either the [Microsoft.Azure.ServiceBus samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) or [Microsoft.ServiceBus.Messaging samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) for an example that uses the .NET Framework client to handle session-aware messages. 

To learn more about Service Bus messaging, see the following topics:

* [Service Bus queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md)
* [Get started with Service Bus queues](service-bus-dotnet-get-started-with-queues.md)
* [How to use Service Bus topics and subscriptions](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
