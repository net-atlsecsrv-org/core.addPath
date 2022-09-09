---
title: Azure Service Bus duplicate message detection | Microsoft Docs
description: This article explains how you can detect duplicates in Azure Service Bus messages. The duplicate message can be ignored and dropped.
ms.topic: article
ms.date: 06/23/2020
---

# Duplicate detection

If an application fails due to a fatal error immediately after it sends a message, and the restarted application instance erroneously believes that the prior message delivery did not occur, a subsequent send causes the same message to appear in the system twice.

It is also possible for an error at the client or network level to occur a moment earlier, and for a sent message to be committed into the queue, with the acknowledgment not successfully returned to the client. This scenario leaves the client in doubt about the outcome of the send operation.

Duplicate detection takes the doubt out of these situations by enabling the sender resend the same message, and the queue or topic discards any duplicate copies.

Enabling duplicate detection helps keep track of the application-controlled *MessageId* of all messages sent into a queue or topic during a specified time window. If any new message is sent with *MessageId* that was logged during the time window, the message is reported as accepted (the send operation succeeds), but the newly sent message is instantly ignored and dropped. No other parts of the message other than the *MessageId* are considered.

Application control of the identifier is essential, because only that allows the application to tie the *MessageId* to a business process context from which it can be predictably reconstructed when a failure occurs.

For a business process in which multiple messages are sent in the course of handling some application context, the *MessageId* may be a composite of the application-level context identifier, such as a purchase order number, and the subject of the message, for example, **12345.2017/payment**.

The *MessageId* can always be some GUID, but anchoring the identifier to the business process yields predictable repeatability, which is desired for leveraging the duplicate detection feature effectively.

> [!NOTE]
> If the duplicate detection is enabled and session ID or partition key are not set, the message ID is used as the partition key. If the message ID is also not set, .NET and AMQP libraries automatically generate a message ID for the message. For more information, see [Use of partition keys](service-bus-partitioning.md#use-of-partition-keys).

## Enable duplicate detection

In the portal, the feature is turned on during entity creation with the **Enable duplicate detection** check box, which is off by default. The setting for creating new topics is equivalent.

![Screenshot of the Create queue dialog box with the Enable duplicate detection option selected and outlined in red.][1]

> [!IMPORTANT]
> You can't enable/disable duplicate detection after the queue is created. You can only do so at the time of creating the queue. 

Programmatically, you set the flag with the [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) property on the full framework .NET API. With the Azure Resource Manager API, the value is set with the [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) property.

The duplicate detection time history defaults to 30 seconds for queues and topics, with a maximum value of seven days. You can change this setting in the queue and topic properties window in the Azure portal.

![Screenshot of the Service Bus feature with the Properties setting highlighted adn the Duplicate detection history option outlined in red.][2]

Programmatically, you can configure the size of the duplicate detection window during which message-ids are retained, using the [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) property with the full .NET Framework API. With the Azure Resource Manager API, the value is set with the [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) property.

Enabling duplicate detection and the size of the window directly impact the queue (and topic) throughput, since all recorded message-ids must be matched against the newly submitted message identifier.

Keeping the window small means that fewer message-ids must be retained and matched, and throughput is impacted less. For high throughput entities that require duplicate detection, you should keep the window as small as possible.

## Next steps

To learn more about Service Bus messaging, see the following topics:

* [Service Bus queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md)
* [Get started with Service Bus queues](service-bus-dotnet-get-started-with-queues.md)
* [How to use Service Bus topics and subscriptions](service-bus-dotnet-how-to-use-topics-subscriptions.md)

In scenarios where client code is unable to resubmit a message with the same *MessageId* as before, it is important to design messages which can be safely re-processed. This [blog post about idempotence](https://particular.net/blog/what-does-idempotent-mean) describes various techniques for how to do that.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
