---
title: Overview of features - Azure Event Hubs | Microsoft Docs
description: This article provides details about features and terminology of Azure Event Hubs. 
ms.topic: article
ms.date: 03/15/2021
---

# Features and terminology in Azure Event Hubs

Azure Event Hubs is a scalable event processing service that ingests and processes large volumes of events and data, with low latency and high reliability. See [What is Event Hubs?](./event-hubs-about.md) for a high-level overview.

This article builds on the information in the [overview article](./event-hubs-about.md), and provides technical and implementation details about Event Hubs components and features.

> [!TIP]
> [The protocol support for **Apache Kafka** clients](event-hubs-for-kafka-ecosystem-overview.md)  (versions >=1.0) provides network endpoints that enable applications built to use Apache Kafka with any client to use Event Hubs. Most existing Kafka applications can simply be reconfigured to point to an Event Hub namespace instead of a Kafka cluster bootstrap server. 
>
>From the perspective of cost, operational effort, and reliability, Azure Event Hubs is a great alternative to deploying and operating your own Kafka and Zookeeper clusters and to Kafka-as-a-Service offerings not native to Azure. 
>
> In addition to getting the same core functionality as of the Apache Kafka broker, you also get access to Azure Event Hub features like automatic batching and archiving via [Event Hubs Capture](event-hubs-capture-overview.md), automatic scaling and balancing, disaster recovery, cost-neutral availability zone support, flexible and secure network integration, and multi-protocol support including the firewall-friendly AMQP-over-WebSockets protocol.


## Namespace
An Event Hubs namespace provides DNS integrated network endpoints and a range of access control and network integration management features such as [IP filtering](event-hubs-ip-filtering.md), [virtual network service endpoint](event-hubs-service-endpoints.md), and [Private Link](private-link-service.md) and is the management container for one of multiple Event Hub instances (or topics, in Kafka parlance).

## Event publishers

Any entity that sends data to an Event Hub is an *event publisher* (synonymously used with *event producer*). Event publishers can publish events using HTTPS or AMQP 1.0 or the Kafka protocol. Event publishers use Azure Active Directory based authorization with OAuth2-issued JWT tokens or an Event Hub-specific Shared Access Signature (SAS) token gain publishing access.

### Publishing an event

You can publish an event via AMQP 1.0, the Kafka protocol, or HTTPS. The Event Hubs service provides [REST API](/rest/api/eventhub/) and [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md), and [Go](event-hubs-go-get-started-send.md) client libraries for publishing events to an event hub. For other runtimes and platforms, you can use any AMQP 1.0 client, such as [Apache Qpid](https://qpid.apache.org/). 

The choice to use AMQP or HTTPS is specific to the usage scenario. AMQP requires the establishment of a persistent bidirectional socket in addition to transport level security (TLS) or SSL/TLS. AMQP has higher network costs when initializing the session, however HTTPS requires additional TLS overhead for every request. AMQP has significantly higher performance for frequent publishers and can achieve much lower latencies when used with asynchronous publishing code.

You can publish events individually or batched. A single publication has a limit of 1 MB, regardless of whether it is a single event or a batch. Publishing events larger than this threshold will be rejected. 

Event Hubs throughput is scaled by using partitions and throughput-unit allocations (see below). It is a best practice for publishers to remain unaware of the specific partitioning model chosen for an Event Hub and to only specify a *partition key* that is used to consistently assign related events to the same partition.

![Partition keys](./media/event-hubs-features/partition_keys.png)

Event Hubs ensures that all events sharing a partition key value are stored together and delivered in order of arrival. If partition keys are used with publisher policies, then the identity of the publisher and the value of the partition key must match. Otherwise, an error occurs.

### Event Retention

Published events are removed from an Event Hub based on a configurable, timed-based retention policy. Here are a few important points:

- The **default** value and **shortest** possible retention period is **1 day (24 hours)**.
- For Event Hubs **Standard**, the maximum retention period is **7 days**. 
- For Event Hubs **Dedicated**, the maximum retention period is **90 days**.
- If you change the retention period, it applies to all messages including messages that are already in the event hub. 

Event Hubs retains events for a configured retention time that applies across
all partitions. Events are automatically removed when the retention period has
been reached. If you specify a retention period of one day, the event will
become unavailable exactly 24 hours after it has been accepted. You cannot
explicitly delete events. 

If you need to archive events beyond the allowed
retention period, you can have them [automatically stored in Azure Storage or
Azure Data Lake by turning on the Event Hubs Capture
feature](event-hubs-capture-overview.md), and if you need
to search or analyze such deep archives, you can [easily import them into Azure
Synapse](store-captured-data-data-warehouse.md) or other
similar stores and analytics platforms. 

The reason for Event Hubs' limit on data retention based on time is to prevent
large volumes of historic customer data getting trapped in a deep store that is
only indexed by a timestamp and only allows for sequential access. The
architectural philosophy here is that historic data needs richer indexing and
more direct access than the real-time eventing interface that Event Hubs or
Kafka provide. Event stream engines are not well suited to play the role of data
lakes or long-term archives for event sourcing. 
 

> [!NOTE]
> Event Hubs is a real-time event stream engine and is not designed to be used instead of a database and/or as a 
> permanent store for infinitely held event streams. 
> 
> The deeper the history of an event stream gets, the more you will need auxiliary indexes to find a particular historical slice of a given stream. Inspection of event payloads and indexing are not within the feature scope of Event Hubs (or Apache Kafka). Databases and specialized analytics stores and engines such as [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) and [Azure Synapse](../synapse-analytics/overview-what-is.md) are therefore far better suited for storing historic events.
>
> [Event Hubs Capture](event-hubs-capture-overview.md) integrates directly with Azure Blob Storage and Azure Data Lake Storage and, through that integration, also enables [flowing events directly into Azure Synapse](store-captured-data-data-warehouse.md).
>
> If you want to use the [Event Sourcing](/azure/architecture/patterns/event-sourcing) pattern for your application, you should align your snapshot strategy with the retention limits of Event Hubs. Do not aim to rebuild materialized views from raw events starting at the beginning of time. You would surely come to regret such a strategy once your application is in production for a while and is well used, and your projection builder has to churn through years of change events while trying to catch up to the latest and ongoing changes. 


### Publisher policy

Event Hubs enables granular control over event publishers through *publisher policies*. Publisher policies are run-time features designed to facilitate large numbers of independent event publishers. With publisher policies, each publisher uses its own unique identifier when publishing events to an event hub, using the following mechanism:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

You don't have to create publisher names ahead of time, but they must match the SAS token used when publishing an event, in order to ensure independent publisher identities. When using publisher policies, the **PartitionKey** value is set to the publisher name. To work properly, these values must match.

## Capture

[Event Hubs Capture](event-hubs-capture-overview.md) enables you to automatically capture the streaming data in Event Hubs and save it to your choice of either a Blob storage account, or an Azure Data Lake Service account. You can enable Capture from the Azure portal, and specify a minimum size and time window to perform the capture. Using Event Hubs Capture, you specify your own Azure Blob Storage account and container, or Azure Data Lake Service account, one of which is used to store the captured data. Captured data is written in the Apache Avro format.

## Partitions
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## SAS tokens

Event Hubs uses *Shared Access Signatures*, which are available at the namespace and event hub level. A SAS token is generated from a SAS key and is an SHA hash of a URL, encoded in a specific format. Using the name of the key (policy) and the token, Event Hubs can regenerate the hash and thus authenticate the sender. Normally, SAS tokens for event publishers are created with only **send** privileges on a specific event hub. This SAS token URL mechanism is the basis for publisher identification introduced in the publisher policy. For more information about working with SAS, see [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md).

## Event consumers

Any entity that reads event data from an event hub is an *event consumer*. All Event Hubs consumers connect via the AMQP 1.0 session and events are delivered through the session as they become available. The client does not need to poll for data availability.

### Consumer groups

The publish/subscribe mechanism of Event Hubs is enabled through *consumer groups*. A consumer group is a view (state, position, or offset) of an entire event hub. Consumer groups enable multiple consuming applications to each have a separate view of the event stream, and to read the stream independently at their own pace and with their own offsets.

In a stream processing architecture, each downstream application equates to a consumer group. If you want to write event data to long-term storage, then that storage writer application is a consumer group. Complex event processing can then be performed by another, separate consumer group. You can only access partitions through a consumer group. There is always a default consumer group in an event hub, and you can create up to 20 consumer groups for a Standard tier event hub.

There can be at most 5 concurrent readers on a partition per consumer group; however **it is recommended that there is only one active receiver on a partition per consumer group**. Within a single partition, each reader receives all of the messages. If you have multiple readers on the same partition, then you process duplicate messages. You need to handle this in your code, which may not be trivial. However, it's a valid approach in some scenarios.

Some clients offered by the Azure SDKs are intelligent consumer agents that automatically manage the details of ensuring that each partition has a single reader and that all partitions for an event hub are being read from. This allows your code to focus on processing the events being read from the event hub so it can ignore many of the details of the partitions. For more information, see [Connect to a partition](#connect-to-a-partition).

The following examples show the consumer group URI convention:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

The following figure shows the Event Hubs stream processing architecture:

![Event Hubs architecture](./media/event-hubs-about/event_hubs_architecture.svg)

### Stream offsets

An *offset* is the position of an event within a partition. You can think of an offset as a client-side cursor. The offset is a byte numbering of the event. This offset enables an event consumer (reader) to specify a point in the event stream from which they want to begin reading events. You can specify the offset as a timestamp or as an offset value. Consumers are responsible for storing their own offset values outside of the Event Hubs service. Within a partition, each event includes an offset.

![Partition offset](./media/event-hubs-features/partition_offset.png)

### Checkpointing

*Checkpointing* is a process by which readers mark or commit their position within a partition event sequence. Checkpointing is the responsibility of the consumer and occurs on a per-partition basis within a consumer group. This responsibility means that for each consumer group, each partition reader must keep track of its current position in the event stream, and can inform the service when it considers the data stream complete.

If a reader disconnects from a partition, when it reconnects it begins reading at the checkpoint that was previously submitted by the last reader of that partition in that consumer group. When the reader connects, it passes the offset to the event hub to specify the location at which to start reading. In this way, you can use checkpointing to both mark events as "complete" by downstream applications, and to provide resiliency if a failover between readers running on different machines occurs. It is possible to return to older data by specifying a lower offset from this checkpointing process. Through this mechanism, checkpointing enables both failover resiliency and event stream replay.

> [!IMPORTANT]
> Offsets are provided by the Event Hubs service. It is the responsibility of the consumer to checkpoint as events are processed.

> [!NOTE]
> If you are using Azure Blob Storage as the checkpoint store in an environment that supports a different version of Storage Blob SDK than those typically available on Azure, you'll need to use code to change the Storage service API version to the specific version supported by that environment. For example, if you are running [Event Hubs on an Azure Stack Hub version 2002](/azure-stack/user/event-hubs-overview), the highest available version for the Storage service is version 2017-11-09. In this case, you need to use code to target the Storage service API version to 2017-11-09. For an example on how to target a specific Storage API version, see these samples on GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) or  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### Common consumer tasks

All Event Hubs consumers connect via an AMQP 1.0 session, a state-aware bidirectional communication channel. Each partition has an AMQP 1.0 session that facilitates the transport of events segregated by partition.

#### Connect to a partition

When connecting to partitions, it's common practice to use a leasing mechanism to coordinate reader connections to specific partitions. This way, it's possible for every partition in a consumer group to have only one active reader. Checkpointing, leasing, and managing readers are simplified by using the clients within the Event Hubs SDKs, which act as intelligent consumer agents. These are:

- The [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) for .NET
- The [EventProcessorClient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventProcessorClient.java) for Java
- The [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) for Python
- The [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) for JavaScript/TypeScript

#### Read events

After an AMQP 1.0 session and link is opened for a specific partition, events are delivered to the AMQP 1.0 client by the Event Hubs service. This delivery mechanism enables higher throughput and lower latency than pull-based mechanisms such as HTTP GET. As events are sent to the client, each event data instance contains important metadata such as the offset and sequence number that are used to facilitate checkpointing on the event sequence.

Event data:
* Offset
* Sequence number
* Body
* User properties
* System properties

It is your responsibility to manage the offset.

## Next steps

For more information about Event Hubs, visit the following links:

- Get started with Event Hubs
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs programming guide](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md)
* [Event Hubs FAQ](event-hubs-faq.yml)
* [Event Hubs samples](event-hubs-samples.md)
