---
title: Overview of features - Azure Event Hubs | Microsoft Docs
description: This article provides details about features and terminology of Azure Event Hubs. 
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt

ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija

---

# Features and terminology in Azure Event Hubs

Azure Event Hubs is a scalable event processing service that ingests and processes large volumes of events and data, with low latency and high reliability. See [What is Event Hubs?](event-hubs-what-is-event-hubs.md) for a high-level overview.

This article builds on the information in the [overview article](event-hubs-what-is-event-hubs.md), and provides technical and implementation details about Event Hubs components and features.

## Namespace
An Event Hubs namespace provides a unique scoping container, referenced by its [fully qualified domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), in which you create one or more event hubs or Kafka topics. 

## Event Hubs for Apache Kafka

[This feature](event-hubs-for-kafka-ecosystem-overview.md) provides an endpoint that enables customers to talk to Event Hubs using the Kafka protocol. This integration provides customers a Kafka endpoint. This enables customers to configure their existing Kafka applications to talk to Event Hubs, giving an alternative to running their own Kafka clusters. Event Hubs for Apache Kafka supports Kafka protocol 1.0 and later. 

With this integration, you don't need to run Kafka clusters or manage them with Zookeeper. This also allows you to work with some of the most demanding features of Event Hubs like Capture, Auto-inflate, and Geo-disaster Recovery.

This integration also allows applications like Mirror Maker or framework like Kafka Connect to work clusterless with just configuration changes. 

## Event publishers

Any entity that sends data to an event hub is an event producer, or *event publisher*. Event publishers can publish events using HTTPS or AMQP 1.0 or Kafka 1.0 and later. Event publishers use a Shared Access Signature (SAS) token to identify themselves to an event hub, and can have a unique identity, or use a common SAS token.

### Publishing an event

You can publish an event via AMQP 1.0, Kafka 1.0 (and later), or HTTPS. Event Hubs provides [client libraries and classes](event-hubs-dotnet-framework-api-overview.md) for publishing events to an event hub from .NET clients. For other runtimes and platforms, you can use any AMQP 1.0 client, such as [Apache Qpid](https://qpid.apache.org/). You can publish events individually, or batched. A single publication (event data instance) has a limit of 1 MB, regardless of whether it is a single event or a batch. Publishing events larger than this threshold results in an error. It is a best practice for publishers to be unaware of partitions within the event hub and to only specify a *partition key* (introduced in the next section), or their identity via their SAS token.

The choice to use AMQP or HTTPS is specific to the usage scenario. AMQP requires the establishment of a persistent bidirectional socket in addition to transport level security (TLS) or SSL/TLS. AMQP has higher network costs when initializing the session, however HTTPS requires additional SSL overhead for every request. AMQP has higher performance for frequent publishers.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs ensures that all events sharing a partition key value are delivered in order, and to the same partition. If partition keys are used with publisher policies, then the identity of the publisher and the value of the partition key must match. Otherwise, an error occurs.

### Publisher policy

Event Hubs enables granular control over event publishers through *publisher policies*. Publisher policies are run-time features designed to facilitate large numbers of independent event publishers. With publisher policies, each publisher uses its own unique identifier when publishing events to an event hub, using the following mechanism:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

You don't have to create publisher names ahead of time, but they must match the SAS token used when publishing an event, in order to ensure independent publisher identities. When using publisher policies, the **PartitionKey** value is set to the publisher name. To work properly, these values must match.

## Capture

[Event Hubs Capture](event-hubs-capture-overview.md) enables you to automatically capture the streaming data in Event Hubs and save it to your choice of either a Blob storage account, or an Azure Data Lake Service account. You can enable Capture from the Azure portal, and specify a minimum size and time window to perform the capture. Using Event Hubs Capture, you specify your own Azure Blob Storage account and container, or Azure Data Lake Service account, one of which is used to store the captured data. Captured data is written in the Apache Avro format.

## Partitions

Event Hubs provides message streaming through a partitioned consumer pattern in which each consumer only reads a specific subset, or partition, of the message stream. This pattern enables horizontal scale for event processing and provides other stream-focused features that are unavailable in queues and topics.

A partition is an ordered sequence of events that is held in an event hub. As newer events arrive, they are added to the end of this sequence. A partition can be thought of as a "commit log."

![Event Hubs](./media/event-hubs-features/partition.png)

Event Hubs retains data for a configured retention time that applies across all partitions in the event hub. Events expire on a time basis; you cannot explicitly delete them. Because partitions are independent and contain their own sequence of data, they often grow at different rates.

![Event Hubs](./media/event-hubs-features/multiple_partitions.png)

The number of partitions is specified at creation and must be between 2 and 32. The partition count is not changeable, so you should consider long-term scale when setting partition count. Partitions are a data organization mechanism that relates to the downstream parallelism required in consuming applications. The number of partitions in an event hub directly relates to the number of concurrent readers you expect to have. You can increase the number of partitions beyond 32 by contacting the Event Hubs team.

While partitions are identifiable and can be sent to directly, sending directly to a partition is not recommended. Instead, you can use higher level constructs introduced in the [Event publisher](#event-publishers) and Capacity sections. 

Partitions are filled with a sequence of event data that contains the body of the event, a user-defined property bag, and metadata such as its offset in the partition and its number in the stream sequence.

For more information about partitions and the trade-off between availability and reliability, see the [Event Hubs programming guide](event-hubs-programming-guide.md#partition-key) and the [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) article.

### Partition key

You can use a [partition key](event-hubs-programming-guide.md#partition-key) to map incoming event data into specific partitions for the purpose of data organization. The partition key is a sender-supplied value passed into an event hub. It is processed through a static hashing function, which creates the partition assignment. If you don't specify a partition key when publishing an event, a round-robin assignment is used.

The event publisher is only aware of its partition key, not the partition to which the events are published. This decoupling of key and partition insulates the sender from needing to know too much about the downstream processing. A per-device or user unique identity makes a good partition key, but other attributes such as geography can also be used to group related events into a single partition.

## SAS tokens

Event Hubs uses *Shared Access Signatures*, which are available at the namespace and event hub level. A SAS token is generated from a SAS key and is an SHA hash of a URL, encoded in a specific format. Using the name of the key (policy) and the token, Event Hubs can regenerate the hash and thus authenticate the sender. Normally, SAS tokens for event publishers are created with only **send** privileges on a specific event hub. This SAS token URL mechanism is the basis for publisher identification introduced in the publisher policy. For more information about working with SAS, see [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md).

## Event consumers

Any entity that reads event data from an event hub is an *event consumer*. All Event Hubs consumers connect via the AMQP 1.0 session and events are delivered through the session as they become available. The client does not need to poll for data availability.

### Consumer groups

The publish/subscribe mechanism of Event Hubs is enabled through *consumer groups*. A consumer group is a view (state, position, or offset) of an entire event hub. Consumer groups enable multiple consuming applications to each have a separate view of the event stream, and to read the stream independently at their own pace and with their own offsets.

In a stream processing architecture, each downstream application equates to a consumer group. If you want to write event data to long-term storage, then that storage writer application is a consumer group. Complex event processing can then be performed by another, separate consumer group. You can only access partitions through a consumer group. There is always a default consumer group in an event hub, and you can create up to 20 consumer groups for a Standard tier event hub.

There can be at most 5 concurrent readers on a partition per consumer group; however **it is recommended that there is only one active receiver on a partition per consumer group**. Within a single partition, each reader receives all of the messages. If you have multiple readers on the same partition, then you process duplicate messages. You need to handle this in your code, which may not be trivial. However, it's a valid approach in some scenarios.


The following are examples of the consumer group URI convention:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

The following figure shows the Event Hubs stream processing architecture:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### Stream offsets

An *offset* is the position of an event within a partition. You can think of an offset as a client-side cursor. The offset is a byte numbering of the event. This offset enables an event consumer (reader) to specify a point in the event stream from which they want to begin reading events. You can specify the offset as a timestamp or as an offset value. Consumers are responsible for storing their own offset values outside of the Event Hubs service. Within a partition, each event includes an offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### Checkpointing

*Checkpointing* is a process by which readers mark or commit their position within a partition event sequence. Checkpointing is the responsibility of the consumer and occurs on a per-partition basis within a consumer group. This responsibility means that for each consumer group, each partition reader must keep track of its current position in the event stream, and can inform the service when it considers the data stream complete.

If a reader disconnects from a partition, when it reconnects it begins reading at the checkpoint that was previously submitted by the last reader of that partition in that consumer group. When the reader connects, it passes the offset to the event hub to specify the location at which to start reading. In this way, you can use checkpointing to both mark events as "complete" by downstream applications, and to provide resiliency if a failover between readers running on different machines occurs. It is possible to return to older data by specifying a lower offset from this checkpointing process. Through this mechanism, checkpointing enables both failover resiliency and event stream replay.

### Common consumer tasks

All Event Hubs consumers connect via an AMQP 1.0 session, a state-aware bidirectional communication channel. Each partition has an AMQP 1.0 session that facilitates the transport of events segregated by partition.

#### Connect to a partition

When connecting to partitions, it is common practice to use a leasing mechanism to coordinate reader connections to specific partitions. This way, it is possible for every partition in a consumer group to have only one active reader. Checkpointing, leasing, and managing readers are simplified by using the [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) class for .NET clients. The Event Processor Host is an intelligent consumer agent.

#### Read events

After an AMQP 1.0 session and link is opened for a specific partition, events are delivered to the AMQP 1.0 client by the Event Hubs service. This delivery mechanism enables higher throughput and lower latency than pull-based mechanisms such as HTTP GET. As events are sent to the client, each event data instance contains important metadata such as the offset and sequence number that are used to facilitate checkpointing on the event sequence.

Event data:
* Offset
* Sequence number
* Body
* User properties
* System properties

It is your responsibility to manage the offset.

## Scaling with Event Hubs

There are two factors which influence scaling with Event Hubs.
*	Throughput units
*	Partitions

### Throughput units

The throughput capacity of Event Hubs is controlled by *throughput units*. Throughput units are pre-purchased units of capacity. A single throughput lets you:

* Ingress: Up to 1 MB per second or 1000 events per second (whichever comes first).
* Egress: Up to 2 MB per second or 4096 events per second.

Beyond the capacity of the purchased throughput units, ingress is throttled and a [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) is returned. Egress does not produce throttling exceptions, but is still limited to the capacity of the purchased throughput units. If you receive publishing rate exceptions or are expecting to see higher egress, be sure to check how many throughput units you have purchased for the namespace. You can manage throughput units on the **Scale** blade of the namespaces in the [Azure portal](https://portal.azure.com). You can also manage throughput units programmatically using the [Event Hubs APIs](event-hubs-api-overview.md).

Throughput units are pre-purchased and are billed per hour. Once purchased, throughput units are billed for a minimum of one hour. Up to 20 throughput units can be purchased for an Event Hubs namespace and are shared across all event hubs in that namespace.

### Partitions

Partitions let you scale for your downstream processing. Because of the partitioned consumer model that Event Hubs offers with partitions, you can scale-out while processing your events concurrently. An Event Hub can have up to 32 partitions.

We recommend that you balance 1:1 throughput units and partitions to achieve optimal scale. A single partition has a guaranteed Ingress and Egress of up to one throughput unit. While you may be able to achieve higher throughput on a partition, performance is not guaranteed. This is why we strongly recommend that the number of partitions in an event hub be greater than or equal to the number of throughput units.

Given the total throughput you plan on needing, you know the number of throughput units you require and the minimum number of partitions, but how many partitions should you have? Choose number of partitions based on the downstream parallelism you want to achieve as well as your future throughput needs. There is no charge for the number of partitions you have within an Event Hub.

For detailed Event Hubs pricing information, see [Event Hubs pricing](https://azure.microsoft.com/pricing/details/event-hubs/).

## Next steps

For more information about Event Hubs, visit the following links:

* Get started with an [Event Hubs tutorial][Event Hubs tutorial]
* [Event Hubs programming guide](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md)
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs samples][]

[Event Hubs tutorial]: event-hubs-dotnet-standard-getstarted-send.md
[Event Hubs samples]: https://github.com/Azure/azure-event-hubs/tree/master/samples
