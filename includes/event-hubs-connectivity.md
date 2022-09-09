---
title: include file
description: include file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: "include file"

---

### What ports do I need to open on the firewall? 
You can use the following protocols with Azure Event Hubs to send and receive events:

- Advanced Message Queuing Protocol 1.0 (AMQP)
- Hypertext Transfer Protocol 1.1 with TLS (HTTPS)
- Apache Kafka

See the following table for the outbound ports you need to open to use these protocols to communicate with Azure Event Hubs. 

| Protocol | Ports | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 and 5672 | See [AMQP protocol guide](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | This port is used for the HTTP/REST API and for AMQP-over-WebSockets. |
| Kafka | 9093 | See [Use Event Hubs from Kafka applications](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

The HTTPS port is required for outbound communication also when AMQP is used over port 5671, because several management operations performed by the client SDKs and the acquisition of tokens from Azure Active Directory (when used) run over HTTPS. 

The official Azure SDKs generally use the AMQP protocol for sending and receiving events from Event Hubs. The AMQP-over-WebSockets protocol option runs over port TCP 443 just like the HTTP API, but is otherwise functionally identical with plain AMQP. This option has higher initial connection latency because of extra handshake round trips and slightly more overhead as tradeoff for sharing the HTTPS port. If this mode is selected, TCP port 443 is sufficient for communication. The following options allow selecting the plain AMQP or AMQP WebSockets mode:

| Language | Option   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions.TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype?view=azure-dotnet&preserve-view=true) property with [EventHubsTransportType.AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) or [EventHubsTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype?view=azure-dotnet&preserve-view=true) |
| Java     | [com.microsoft.azure.eventhubs.EventProcessorClientBuilder.transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype?view=azure-java-stable&preserve-view=true) with [AmqpTransportType.AMQP](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) or [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype?view=azure-java-stable&preserve-view=true) |
| Node  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions?view=azure-node-latest&preserve-view=true) has a `webSocketOptions` property. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient?view=azure-python&preserve-view=true) with [TransportType.Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python) or [TransportType.AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype?view=azure-python&preserve-view=true) |

### What IP addresses do I need to allow?
When you are working with Azure, sometimes you have to allow specific IP address ranges or URLs in your corporate firewall or proxy to access all Azure services you are using or trying to use. Verify that the traffic is allowed on IP addresses used by Event Hubs. For IP addresses used by Azure Event Hubs: see [Azure IP Ranges and Service Tags - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Also, verify that the IP address for your namespace is allowed. To find the right IP addresses to allow for your connections, follow these steps:

1. Run the following command from a command prompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Note down the IP address returned in `Non-authoritative answer`. 

If you use the **zone redundancy** for your namespace, you need to do a few additional steps: 

1. First, you run nslookup on the namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Note down the name in the **non-authoritative answer** section, which is in one of the following formats: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Run nslookup for each one with suffixes s1, s2, and s3 to get the IP addresses of all three instances running in three availability zones, 

    > [!NOTE]
    > The IP address returned by the `nslookup` command isn't a static IP address. However, it remains constant until the underlying deployment is deleted or moved to a different cluster.

### Where can I find client IP sending or receiving messages to my namespace?
First, enable [IP filtering](../articles/event-hubs/event-hubs-ip-filtering.md) on the namespace. 

Then, Enable diagnostic logs for [Event Hubs virtual network connection events](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) by following instructions in the [Enable diagnostic logs](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). You'll see the IP address for which connection is denied.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Virtual network logs are generated only if the namespace allows access from **specific IP addresses** (IP filter rules). If you don't want to restrict access to your namespace using these features and still want to get virtual network logs to track IP addresses of clients connecting to the Event Hubs namespace, you could use the following workaround: Enable IP filtering, and add the total addressable IPv4 range (1.0.0.0/1 - 255.0.0.0/1). Event Hubs doesn't support IPv6 address ranges. 
