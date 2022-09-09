---
title: Use Java Message Service 2.0 API with Azure Service Bus Premium
description: How to use the Java Message Service (JMS) with Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
---

# Use Java Message Service 2.0 API with Azure Service Bus Premium (Preview)

This article explains how to use the popular **Java Message Service (JMS) 2.0** API to interact with Azure Service Bus over the Advanced Message Queueing Protocol (AMQP 1.0) protocol.

> [!NOTE]
> Support for Java Message Service (JMS) 2.0 API is only available on the **Azure Service Bus Premium tier** and is currently in **preview**.
>

## Get started with Service Bus

This guide assumes that you already have a Service Bus namespace. If you don't, then you can [create the namespace and queue](service-bus-create-namespace-portal.md) using the [Azure portal](https://portal.azure.com). 

For more information about how to create Service Bus namespaces and queues, see [Get started with Service Bus queues through the Azure portal](service-bus-quickstart-portal.md).

## What JMS features are supported?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## Downloading the Java Message Service (JMS) client library

To utilize all the features available on Azure Service Bus Premium tier, the below library must be added to the build path of the project.

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> To add the [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) to the build path, utilize the preferred dependency management tool for your project like [Maven](https://maven.apache.org/) or [Gradle](https://gradle.org/).
>

## Coding Java applications

Once the dependencies have been imported, the Java applications can be written in a JMS provider agnostic manner.

### Connecting to Azure Service Bus using JMS

To connect with Azure Service Bus using JMS clients, you need the **connection string** that is available in the 'Shared Access Policies' in the [Azure portal](https://portal.azure.com) under **Primary Connection String**.

1. Instantiate the `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instantiate the `ServiceBusJmsConnectionFactory` with the appropriate `ServiceBusConnectionString`.

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Use the `ConnectionFactory` to either create a `Connection` and then a `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    or a `JMSContext` (for JMS 2.0 clients)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### Write the JMS application

Once the `Session` or `JMSContext` has been instantiated, your application can use the familiar JMS APIs to perform both management and data operations.

Refer to the list of [supported JMS features](how-to-use-java-message-service-20.md#what-jms-features-are-supported) to see which APIs are supported as part of this preview.

Below are some sample code snippets to get started with JMS -

#### Sending messages to a queue and topic

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### Receiving messages from a queue

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### Receiving messages from a shared durable subscription on a topic

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## Summary

This guide showcased how Java client applications using Java Message Service (JMS) over AMQP 1.0 can interact with Azure Service Bus.

You can also use Service Bus AMQP 1.0 from other languages, including .NET, C, Python, and PHP. Components built using these different languages can exchange messages reliably and at full fidelity using the AMQP 1.0 support in Service Bus.

## Next steps

For more information on Azure Service Bus and details about Java Message Service (JMS) entities, check out the links below - 
* [Service Bus - Queues, Topics, and Subscriptions](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service entities](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [AMQP 1.0 support in Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 Developer's Guide](service-bus-amqp-dotnet.md)
* [Get started with Service Bus queues](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API(external Oracle doc)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Learn how to migrate from ActiveMQ to Service Bus](migrate-jms-activemq-to-servicebus.md)
