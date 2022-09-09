---
title: Data partitioning in Azure Cosmos DB Gremlin API
description: Learn how you can use a partitioned graph in Azure Cosmos DB. This article also describes the requirements and best practices for a partitioned graph.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
---
# Using a partitioned graph in Azure Cosmos DB

One of the key features of the Gremlin API in Azure Cosmos DB is the ability to handle large-scale graphs through horizontal scaling. Horizontal scaling is achieved through the [partitioning capabilities in Azure Cosmos DB](partition-data.md). The containers can scale independently in terms of storage and throughput. You can create containers in Azure Cosmos DB that can be automatically scaled to store a graph data. The data is automatically balanced based on the specified **partition key**.

In this document, the specifics on how graph databases are partitioned will be described along with its implications for both vertices (or nodes) and edges.

## Requirements for partitioned graph

The following are details that need to be understood when creating a partitioned graph container:

- **Partitioning is required** if the container is expected to store more than 10 GB in size or if you want to allocate more than 10,000 request units per second (RUs).

- **Both vertices and edges are stored as JSON documents**.

- **Vertices require a partition key**. This key will determine in which partition the vertex will be stored through a hashing algorithm. The name of this partition key is a single-word string without spaces or special characters. The partition key is defined when creating a new container and it has a format: `/partitioning-key-name`.

- **Edges will be stored with their source vertex**. In other words, for each vertex its partition key defines where they are stored along with its outgoing edges. This is done to avoid cross-partition queries when using the `out()` cardinality in graph queries.

- **Graph queries need to specify a partition key**. To take full advantage of the horizontal partitioning in Azure Cosmos DB, the partition key should be specified when a single vertex is selected, whenever it's possible. The following are queries for selecting one or multiple vertices in a partitioned graph:

    - `/id` and `/label` are not supported as partition keys for a container in Gremlin API.


    - Selecting a vertex by ID, then **using the `.has()` step to specify the partition key property**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Selecting a vertex by **specifying a tuple including partition key value and ID**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Specifying an **array of tuples of partition key values and IDs**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Selecting a set of vertices and **specifying a list of partition key values**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## Best practices when using a partitioned graph

Use the following guidelines to ensure performance and scalability when using partitioned graphs with unlimited containers:

- **Always specify the partition key value when querying a vertex**. Getting vertex from a known partition is a way to achieve performance.

- **Use the outgoing direction when querying edges whenever it's possible**. As mentioned above, edges are stored with their source vertices in the outgoing direction. So the chances of resorting to cross-partition queries are minimized when the data and queries are designed with this pattern in mind.

- **Choose a partition key that will evenly distribute data across partitions**. This decision heavily depends on the data model of the solution. Read more about creating an appropriate partition key in [Partitioning and scale in Azure Cosmos DB](partition-data.md).

- **Optimize queries to obtain data within the boundaries of a partition**. An optimal partitioning strategy would be aligned to the querying patterns. Queries that obtain data from a single partition provide the best possible performance.

## Next steps

Next you can proceed to read the following articles:

* Learn about [Partition and scale in Azure Cosmos DB](partition-data.md).
* Learn about the [Gremlin support in Gremlin API](gremlin-support.md).
* Learn about [Introduction to Gremlin API](graph-introduction.md).
