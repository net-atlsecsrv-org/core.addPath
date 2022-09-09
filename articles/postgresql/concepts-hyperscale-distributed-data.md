---
title: Distributed data in Azure Database for PostgreSQL – Hyperscale (Citus) (preview)
description: Tables and shards distributed in the server group.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
---

# Distributed data in Azure Database for PostgreSQL – Hyperscale (Citus) (preview)

This article outlines the three table types in Hyperscale (Citus).
It shows how distributed tables are stored as shards, and the way
shards are placed on nodes.

## Table types

There are three types of tables in a Hyperscale server group, each
used for different purposes.

### Type 1: distributed tables

The first type, and most common, is *distributed* tables. They
appear to be normal tables to SQL statements, but are horizontally
*partitioned* across worker nodes. What this means is that the rows
of the table are stored on different nodes, in fragment tables called
*shards*.

Hyperscale runs not only SQL but DDL statements throughout a cluster,
so changing the schema of a distributed table cascades to update
all the table's shards across workers.

#### Distribution column

Hyperscale uses algorithmic sharding to assign rows to shards. The assignment is made deterministically based on the value
of a table column called the *distribution column.* The cluster
administrator must designate this column when distributing a table.
Making the right choice is important for performance and functionality.

### Type 2: reference tables

A reference table is a type of distributed table whose entire
contents are concentrated into a single shard. The shard is replicated on every worker, so queries on any worker can access the reference
information locally, without the network overhead of requesting
rows from another node. Reference tables have no distribution column
because there is no need to distinguish separate shards per row.

Reference tables are typically small, and are used to store data that is
relevant to queries running on any worker node. For example, enumerated
values like order statuses, or product categories.

### Type 3: local tables

When you use Hyperscale, the coordinator node you connect to is a regular PostgreSQL database. You can create ordinary tables on the coordinator and choose
not to shard them.

A good candidate for local tables would be small administrative tables that don't participate in join queries. For example, a users table for application login and authentication.

## Shards

The previous section described how distributed tables are stored as shards on
worker nodes. This section gets more into the technical details.

The `pg_dist_shard` metadata table on the coordinator contains a
row for each shard of each distributed table in the system. The row
matches a shard ID with a range of integers in a hash space
(shardminvalue, shardmaxvalue):

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

If the coordinator node wants to determine which shard holds a row of
`github_events`, it hashes the value of the distribution column in the
row, and checks which shard\'s range contains the hashed value. (The
ranges are defined so that the image of the hash function is their
disjoint union.)

### Shard placements

Suppose that shard 102027 is associated with the row in question. The row
will be read or written in a table called `github_events_102027` in one of
the workers. Which worker? That is determined entirely by the metadata
tables, and the mapping of shard to worker is known as the shard *placement*.

The coordinator node
rewrites queries into fragments that refer to the specific tables
like `github_events_102027`, and runs those fragments on the
appropriate workers. Here is an example of a query run behind the scenes to find the node holding shard ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## Next steps
- Learn to [choose a distribution column](concepts-hyperscale-choose-distribution-column.md) for distributed tables
