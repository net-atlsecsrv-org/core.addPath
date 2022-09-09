---
title: FAQ about Apache Kafka in Azure HDInsight
description: Get answers to common questions about Apache Kafka on Azure HDInsight, a managed Hadoop cloud service.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
---
# Frequently asked questions about Apache Kafka in Azure HDInsight

This article addresses some common questions about using Apache Kafka on Azure HDInsight.

## What Kafka versions are supported by HDInsight?

Find more information about HDInsight officially supported component versions in [What are the Apache Hadoop components and versions available with HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). We recommend always using the latest version to ensure the best possible performance and user experience.

## What resources are provided in an HDInsight Kafka cluster and what resources am I charged for?

A HDInsight Kafka cluster includes the following resources:

* Head nodes
* Zookeeper nodes
* Broker (worker) nodes 
* Azure Managed Disks attached to the broker nodes
* Gateway nodes

All of these resources are charged based on our [HDInsight pricing model](https://azure.microsoft.com/pricing/details/hdinsight/), except for gateway nodes. You are not charged for gateway nodes.

For a more detailed description of various node types, see [Azure HDInsight virtual network architecture](../hdinsight-virtual-network-architecture.md). Pricing is based on per minute node usage. Prices vary depending on node size, number of nodes, type of managed disk used, and region.

## Do Apache Kafka APIs work with HDInsight?

Yes, HDInsight uses native Kafka APIs. Your client application code doesn't need to change. See [Tutorial: Use the Apache Kafka Producer and Consumer APIs](./apache-kafka-producer-consumer-api.md) to see how you can use Java-based producer/consumer APIs with your cluster.

## Can I change cluster configurations?

Yes, through the Ambari portal. Each component in the portal has a **configs** section, which can be used to change component configurations. Some changes may require broker restarts.

## What type of authentication does HDInsight support for Apache Kafka?

Using [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md), you can get topic-level security for their Kafka clusters. See [Tutorial: Configure Apache Kafka policies in HDInsight with Enterprise Security Package (Preview)](../domain-joined/apache-domain-joined-run-kafka.md), for more information.

## Is my data encrypted? Can I use my own keys?

All Kafka messages on the managed disks are encrypted with [Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Data-in-transit (for example, data being transmitted from clients to brokers and the other way around) isn't encrypted by default. It's possible to encrypt such traffic by [setting up SSL on your own](./apache-kafka-ssl-encryption-authentication.md). Additionally, HDInsight allows you to manage their own keys to encrypt the data at rest. See [Bring your own key for Apache Kafka on Azure HDInsight](apache-kafka-byok.md), for more information.

## How do I connect clients to my cluster?

For Kafka clients to communicate with Kafka brokers, they must be able to reach the brokers over the network. For HDInsight clusters, the Virtual Network (VNet) is the security boundary. Hence, the easiest way to connect clients to your HDInsight cluster is to create clients within the same VNet as the cluster. Other scenarios include:

* Connecting clients in a different Azure VNet – Peer the cluster VNet and the client VNet and configure the cluster for [IP Advertising](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). When using IP advertising, Kafka clients must use Broker IP addresses to connect with the brokers, instead of Fully Qualified Domain Names (FQDNs).

* Connecting on-premises clients – Using a VPN network and setting up custom DNS servers as described in [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Creating a public endpoint for your Kafka service – If your enterprise security requirements allow it, you can deploy a public endpoint for your Kafka brokers, or a self-managed open-source REST end point with a public endpoint.

## Can I add more disk space on an existing cluster?

To increase the amount of space available for Kafka messages, you can increase the number of nodes. Currently, adding more disks to an existing cluster isn't supported.

## Can a Kafka cluster work with Databricks? 

Yes, Kafka clusters can work with Databricks so long as they are in the same VNet. To use a Kafka cluster with Databricks, create a VNet with an HDInsight Kafka cluster in it, then specify that VNet when you create your Databricks workspace and use VNet injection. For more information, see [Deploy Azure Databricks in your Azure Virtual Network (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). You will need to provide the bootstrap broker names of the Kafka cluster when creating the Databricks workspace. For information on retrieving the Kafka broker names, see [Get the Apache Zookeeper and Broker host information](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## How can I have maximum data durability?

Data durability allows you to achieve the lowest risk of message loss. In order to achieve maximum data durability, we recommend the following settings:

* use a minimum replication factor of 3 in most regions
* use a minimum replication factor of 4 in regions with only two fault domains
* disable unclean leader elections
* set **min.insync.replicas** to 2 or more - this changes the number of replicas which must be completely in sync with the leader before a write can proceed
* set the **acks** property to **all** - this property requires all replicas to acknowledge all messages

Configuring Kafka for higher data consistency affects the availability of brokers to produce requests.

## Can I replicate my data to multiple clusters?

Yes, data can be replicated to multiple clusters using Kafka MirrorMaker. See details on setting up MirrorMaker can be found in [Mirror Apache Kafka topics](apache-kafka-mirroring.md). Additionally, there are other self-managed open-source technologies and vendors that can help achieve replication to multiple clusters such as [Brooklin](https://github.com/linkedin/Brooklin/).

## Can I upgrade my cluster? How should I upgrade my cluster?

We don't currently support in-place cluster version upgrades. To update your cluster to a higher Kafka version, create a new cluster with the version that you want and migrate your Kafka clients to use the new cluster.

## How do I monitor my Kafka cluster?

Use Azure monitor to analyze your [Kafka logs](./apache-kafka-log-analytics-operations-management.md).

## Next steps

* [Set up Secure Sockets Layer (SSL) encryption and authentication for Apache Kafka in Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Use MirrorMaker to replicate Apache Kafka topics with Kafka on HDInsight](./apache-kafka-mirroring.md)
