---
title: Introduction to the Azure Cosmos DB Cassandra API
description: Learn how you can use Azure Cosmos DB to "lift-and-shift" existing applications and build new applications by using the Cassandra drivers and CQL  
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
---

# Introduction to the Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API can be used as the data store for apps written for [Apache Cassandra](https://cassandra.apache.org). This means that by using existing [Apache drivers](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) compliant with CQLv4, your existing Cassandra application can now communicate with the Azure Cosmos DB Cassandra API. In many cases, you can switch from using Apache Cassandra to using Azure Cosmos DB's Cassandra API, by just changing a connection string. 

The Cassandra API enables you to interact with data stored in Azure Cosmos DB using the Cassandra Query Language (CQL) , Cassandra-based tools (like cqlsh) and Cassandra client drivers that you're already familiar with.

> [!NOTE]
> The [serverless capacity mode](serverless.md) is now available on Azure Cosmos DB's Cassandra API.

## What is the benefit of using Apache Cassandra API for Azure Cosmos DB?

**No operations management**: As a fully managed cloud service, Azure Cosmos DB Cassandra API removes the overhead of managing and monitoring a myriad of settings across OS, JVM, and yaml files and their interactions. Azure Cosmos DB provides monitoring of throughput, latency, storage, availability, and configurable alerts.

**Open source standard**: Despite being a fully managed service, Cassandra API still supports a large surface area of the native [Apache Cassandra wire protocol](cassandra-support.md), allowing you to build applications on a widely used and cloud agnostic open source standard.

**Performance management**: Azure Cosmos DB provides guaranteed low latency reads and writes at the 99th percentile, backed up by the SLAs. Users do not have to worry about operational overhead to ensure high performance and low latency reads and writes. This means that users do not need to deal with scheduling compaction, managing tombstones, setting up bloom filters and replicas manually. Azure Cosmos DB removes the overhead to manage these issues and lets you focus on the application logic.

**Ability to use existing code and tools**: Azure Cosmos DB provides wire protocol level compatibility with existing Cassandra SDKs and tools. This compatibility ensures you can use your existing codebase with Azure Cosmos DB Cassandra API with trivial changes.

**Throughput and storage elasticity**: Azure Cosmos DB provides throughput across all regions and can scale the provisioned throughput with Azure portal, PowerShell, or CLI operations. You can [elastically scale](manage-scale-cassandra.md) storage and throughput for your tables as needed with predictable performance.

**Global distribution and availability**: Azure Cosmos DB provides the ability to globally distribute data across all Azure regions and serve the data locally while ensuring low latency data access and high availability. Azure Cosmos DB provides 99.99% high availability within a region and 99.999% read and write availability across multiple regions with no operations overhead. Learn more in [Distribute data globally](distribute-data-globally.md) article. 

**Choice of consistency**: Azure Cosmos DB provides the choice of five well-defined consistency levels to achieve optimal tradeoffs between consistency and performance. These consistency levels are strong, bounded-staleness, session, consistent prefix and eventual. These well-defined, practical, and intuitive consistency levels allow developers to make precise trade-offs between consistency, availability, and latency. Learn more in [consistency levels](consistency-levels.md) article. 

**Enterprise grade**: Azure cosmos DB provides [compliance certifications](https://www.microsoft.com/trustcenter) to ensure users can use the platform securely. Azure Cosmos DB also provides encryption at rest and in motion, IP firewall, and audit logs for control plane activities.

**Event Sourcing**: Cassandra API provides access to a persistent change log, the [Change Feed](cassandra-change-feed.md), which can facilitate event sourcing directly from the database. In Apache Cassandra, the only equivalent is change data capture (CDC), which is merely a mechanism to flag specific tables for archival as well as rejecting writes to those tables once a configurable size-on-disk for the CDC log is reached (these capabilities are redundant in Cosmos DB as the relevant aspects are automatically governed).

## Next steps

* You can quickly get started with building the following language-specific apps to create and manage Cassandra API data:
  - [Node.js app](create-cassandra-nodejs.md)
  - [.NET app](create-cassandra-dotnet.md)
  - [Python app](create-cassandra-python.md)

* Get started with [creating a Cassandra API account, database, and a table](create-cassandra-api-account-java.md) by using a Java application.

* [Load sample data to the Cassandra API table](cassandra-api-load-data.md) by using a Java application.

* [Query data from the Cassandra API account](cassandra-api-query-data.md) by using a Java application.

* To learn about Apache Cassandra features supported by Azure Cosmos DB Cassandra API, see [Cassandra support](cassandra-support.md) article.

* Read the [Frequently Asked Questions](cassandra-faq.md).
