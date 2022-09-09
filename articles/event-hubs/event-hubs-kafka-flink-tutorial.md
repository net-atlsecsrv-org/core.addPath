---
title: Use Apache Flink for Apache Kafka - Azure Event Hubs | Microsoft Docs
description: This article provides information on how to connect Apache Flink to an Apache Kafka enabled Azure event hub
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt

ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija

---

# Use Apache Flink with Azure Event Hubs for Apache Kafka
This tutorial shows you how to connect Apache Flink to Kafka-enabled event hubs without changing your protocol clients or running your own clusters. Azure Event Hubs supports [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html).

One of the key benefits of using Apache Kafka is the ecosystem of frameworks it can connect to. Kafka enabled Event Hubs combines the flexibility of Kafka with the scalability, consistency, and support of the Azure ecosystem.

In this tutorial, you learn how to:
> [!div class="checklist"]
> * Create an Event Hubs namespace
> * Clone the example project
> * Run Flink producer 
> * Run Flink consumer

> [!NOTE]
> This sample is available on [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## Prerequisites

To complete this tutorial, make sure you have the following prerequisites:

* Read through the [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) article. 
* An Azure subscription. If you do not have one, create a [free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * On Ubuntu, run `apt-get install default-jdk` to install the JDK.
    * Be sure to set the JAVA_HOME environment variable to point to the folder where the JDK is installed.
* [Download](https://maven.apache.org/download.cgi) and [install](https://maven.apache.org/install.html) a Maven binary archive
    * On Ubuntu, you can run `apt-get install maven` to install Maven.
* [Git](https://www.git-scm.com/downloads)
    * On Ubuntu, you can run `sudo apt-get install git` to install Git.

## Create an Event Hubs namespace

An Event Hubs namespace is required to send or receive from any Event Hubs service. See [Create Kafka Enabled Event Hubs](event-hubs-create-kafka-enabled.md) for information about getting an Event Hubs Kafka endpoint. Make sure to copy the Event Hubs connection string for later use.

## Clone the example project

Now that you have a Kafka-enabled Event Hubs connection string, clone the Azure Event Hubs for Kafka repository and navigate to the `flink` subfolder:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## Run Flink producer

Using the provided Flink producer example, send messages to the Event Hubs service.

### Provide an Event Hubs Kafka endpoint

#### producer.config

Update the `bootstrap.servers` and `sasl.jaas.config` values in `producer/src/main/resources/producer.config` to direct the producer to the Event Hubs Kafka endpoint with the correct authentication.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### Run producer from the command line

To run the producer from the command line, generate the JAR and then run from within Maven (or generate the JAR using Maven, then run in Java by adding the necessary Kafka JAR(s) to the classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

The producer will now begin sending events to the Kafka enabled Event Hub at topic `test` and printing the events to stdout.

## Run Flink consumer

Using the provided consumer example, receive messages from the Kafka enabled Event Hubs.

### Provide an Event Hubs Kafka endpoint

#### consumer.config

Update the `bootstrap.servers` and `sasl.jaas.config` values in `consumer/src/main/resources/consumer.config` to direct the consumer to the Event Hubs Kafka endpoint with the correct authentication.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### Run consumer from the command line

To run the consumer from the command line, generate the JAR and then run from within Maven (or generate the JAR using Maven, then run in Java by adding the necessary Kafka JAR(s) to the classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

If the Kafka-enabled event hub has events (for example, if your producer is also running), then the consumer now begins receiving events from the topic `test`.

Check out [Flink's Kafka Connector Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) for more detailed information about connecting Flink to Kafka.

## Next steps
In this tutorial, your learned how to connect Apache Flink to Kafka-enabled event hubs without changing your protocol clients or running your own clusters. You performed the following steps as part of this tutorial: 

> [!div class="checklist"]
> * Create an Event Hubs namespace
> * Clone the example project
> * Run Flink producer 
> * Run Flink consumer

To learn more about Event Hubs and Event Hubs for Kafka, see the following topic:  

- [Learn about Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md)
- [Stream into Event Hubs from your Kafka applications](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mirror a Kafka broker in a Kafka-enabled event hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md)
- [Connect Akka Streams to a Kafka-enabled event hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
