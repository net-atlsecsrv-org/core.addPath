---
title: Troubleshoot common errors in Azure Cosmos DB Cassandra API
description: This doc discusses the ways to troubleshoot common issues encountered in Azure Cosmos DB Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra

---

# Troubleshoot common issues in Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API in Azure Cosmos DB is a compatibility layer, which provides [wire protocol support](cassandra-support.md) for the popular open-source Apache Cassandra database, and is powered by [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). As a fully managed cloud-native service, Azure Cosmos DB provides [guarantees on availability, throughput, and consistency](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) for Cassandra API. These guarantees are not possible in legacy implementations of Apache Cassandra. Cassandra API also facilitates zero-maintenance platform operations, and zero-downtime patching. As such, many of it's backend operations are different from Apache Cassandra, so we recommend particular settings and approaches to avoid common errors. 

This article describes common errors and solutions for applications consuming Azure Cosmos DB Cassandra API.

## Common errors and solutions

| Error               |  Description             | Solution  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | The total number of request units consumed is more than the request-units provisioned on the keyspace or table. So the requests are throttled. | Consider scaling the throughput assigned to a keyspace or table from the Azure portal (see [here](manage-scale-cassandra.md) for scaling operations in Cassandra API) or you can implement a retry policy. For Java, see retry samples for [v3.x driver](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) and [v4.x driver](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). See also [Azure Cosmos Cassandra Extensions for Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| OverloadedException (Java) even with sufficient throughput | The system appears to be throttling requests despite sufficient throughput being provisioned for request volume and/or consumed request unit cost  | Cassandra API implements a system throughput budget for schema-level operations (CREATE TABLE, ALTER TABLE, DROP TABLE). This budget should be enough for schema operations in a production system. However, if you have a high number of schema-level operations, it is possible you are exceeding this limit. As this budget is not user controlled, you will need to consider lowering the number of schema operations being run. If taking this action does not resolve the issue, or it is not feasible for your workload, [create an Azure support request](../azure-portal/supportability/how-to-create-azure-support-request.md).|
| ClosedConnectionException (Java) | After a period of idle time following successful connections, application is unable to connect| This error could be due to idle timeout of Azure LoadBalancers, which is 4 minutes. Set keep alive setting in driver (see below) and increase keep-alive settings in operating system, or [adjust idle timeout in Azure Load Balancer](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal). |
| Other intermittent connectivity errors (Java) | Connection drops or times out unexpectedly | The Apache Cassandra drivers for Java provide two native reconnection policies: `ExponentialReconnectionPolicy` and `ConstantReconnectionPolicy`. The default is `ExponentialReconnectionPolicy`. However, for Azure Cosmos DB Cassandra API, we recommend `ConstantReconnectionPolicy` with a delay of 2 seconds. See the [driver documentation](https://docs.datastax.com/developer/java-driver/4.9/manual/core/reconnection/)  for Java v4.x driver, and [here](https://docs.datastax.com/developer/java-driver/3.7/manual/reconnection/) for Java 3.x guidance (see also the examples below).|

If your error is not listed above, and you are experiencing an error when executing a [supported operation in Cassandra API](cassandra-support.md), where the error is *not present when using native Apache Cassandra*, [create an Azure support request](../azure-portal/supportability/how-to-create-azure-support-request.md)

## Configuring ReconnectionPolicy for Java Driver

### Version 3.x

For version 3.x of the Java driver, configure the reconnection policy when creating a cluster object:

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### Version 4.x

For version 4.x of the Java driver, configure the reconnection policy by overriding settings in `reference.conf` file:

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## Enable keep-alive for Java Driver

### Version 3.x

For version 3.x of the Java driver, set keep-alive when creating a Cluster object, and ensure keep-alive is [enabled in the operating system](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### Version 4.x

For version 4.x of the Java driver, set keep-alive by overriding settings in `reference.conf` and ensure keep-alive is [enabled in the operating system](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089):

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## Next steps

- Learn about the [supported features](cassandra-support.md) in Azure Cosmos DB Cassandra API.
- Learn how to [migrate from native Apache Cassandra to Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md)

