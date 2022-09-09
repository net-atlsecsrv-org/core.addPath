---
title: Manage indexing in Azure Cosmos DB's API for MongoDB
description: This article presents an overview of Azure Cosmos DB indexing capabilities using Azure Cosmos DB's API for MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 08/07/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-js
---
# Manage indexing in Azure Cosmos DB's API for MongoDB

Azure Cosmos DB's API for MongoDB takes advantage of the core index-management capabilities of Azure Cosmos DB. This article focuses on how to add indexes using Azure Cosmos DB's API for MongoDB. You can also read an [overview of indexing in Azure Cosmos DB](index-overview.md) that's relevant across all APIs.

## Indexing for MongoDB server version 3.6

Azure Cosmos DB's API for MongoDB server version 3.6 automatically indexes the `_id` field, which can't be dropped. It automatically enforces the uniqueness of the `_id` field per shard key. In Azure Cosmos DB's API for MongoDB, sharding and indexing are separate concepts. You don't have to index your shard key. However, as with any other property in your document, if this property is a common filter in your queries, we recommend to index the shard key.

To index additional fields, you apply the MongoDB index-management commands. As in MongoDB, Azure Cosmos DB's API for MongoDB automatically indexes the `_id` field only. This default indexing policy differs from the Azure Cosmos DB SQL API, which indexes all fields by default.

To apply a sort to a query, you must create an index on the fields used in the sort operation.

## Index types

### Single field

You can create indexes on any single field. The sort order of the single field index does not matter. The following command creates an index on the field `name`:

`db.coll.createIndex({name:1})`

One query uses multiple single field indexes where available. You can create up to 500 single field indexes per container.

### Compound indexes (MongoDB server version 3.6)

Azure Cosmos DB's API for MongoDB supports compound indexes for accounts that use the version 3.6 wire protocol. You can include up to eight fields in a compound index. **Unlike in MongoDB, you should create a compound index only if your query needs to sort efficiently on multiple fields at once.** For queries with multiple filters that don't need to sort, create multiple single field indexes instead of a single compound index.

The following command creates a compound index on the fields `name` and `age`:

`db.coll.createIndex({name:1,age:1})`

You can use compound indexes to sort efficiently on multiple fields at once, as shown in the following example:

`db.coll.find().sort({name:1,age:1})`

You can also use the preceding compound index to efficiently sort on a query with the opposite sort order on all fields. Here's an example:

`db.coll.find().sort({name:-1,age:-1})`

However, the sequence of the paths in the compound index must exactly match the query. Here's an example of a query that would require an additional compound index:

`db.coll.find().sort({age:1,name:1})`

> [!NOTE]
> You can't create compound indexes on nested properties or arrays.

### Multikey indexes

Azure Cosmos DB creates multikey indexes to index content stored in arrays. If you index a field with an array value, Azure Cosmos DB automatically indexes every element in the array.

### Geospatial indexes

Many geospatial operators will benefit from geospatial indexes. Currently, Azure Cosmos DB's API for MongoDB supports `2dsphere` indexes. The API does not yet support `2d` indexes.

Here's an example of creating a geospatial index on the `location` field:

`db.coll.createIndex({ location : "2dsphere" })`

### Text indexes

Azure Cosmos DB's API for MongoDB does not currently support text indexes. For text search queries on strings, you should use [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integration with Azure Cosmos DB.

## Wildcard indexes

You can use wildcard indexes to support queries against unknown fields. Let's imagine you have a collection that holds data about families.

Here is part of an example document in that collection:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Here's another example , this time with a slightly different set of properties in `children`:

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

In this collection, documents can have many different possible properties. If you wanted to index all the data in the `children` array, you have two options: create separate indexes for each individual property or create one wildcard index for the entire `children` array.

### Create a wildcard index

The following command creates a wildcard index on any properties within `children`:

`db.coll.createIndex({"children.$**" : 1})`

**Unlike in MongoDB, wildcard indexes can support multiple fields in query predicates**. There will not be a difference in query performance if you use one single wildcard index instead of creating a separate index for each property.

You can create the following index types using wildcard syntax:

- Single field
- Geospatial

### Indexing all properties

Here's how you can create a wildcard index on all fields:

`db.coll.createIndex( { "$**" : 1 } )`

As you are starting development, it may be useful to create a wildcard index on all fields. As more properties are indexed in a document, the Request Unit (RU) charge for writing and updating the document will increase. Therefore, if you have a write-heavy workload, you should opt to individually index paths as opposed to using wildcard indexes.

### Limitations

Wildcard indexes do not support any of the following index types or properties:

- Compound
- TTL
- Unique

**Unlike in MongoDB**, in Azure Cosmos DB's API for MongoDB you **can't** use wildcard indexes for:

- Creating a wildcard index that includes multiple specific fields

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Creating a wildcard index that excludes multiple specific fields

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

As an alternative, you could create multiple wildcard indexes.

## Index properties

The following operations are common for accounts serving wire protocol version 3.6 and accounts serving earlier versions. You can learn more about [supported indexes and indexed properties](mongodb-feature-support-36.md#indexes-and-index-properties).

### Unique indexes

[Unique indexes](unique-keys.md) are useful for enforcing that two or more documents do not contain the same value for indexed fields.

> [!IMPORTANT]
> Unique indexes can be created only when the collection is empty (contains no documents).

The following command creates a unique index on the field `student_id`:

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

For sharded collections, you must provide the shard (partition) key  to create a unique index. In other words, all unique indexes on a sharded collection are compound indexes where one of the fields is the partition key.

The following commands create a sharded collection ```coll``` (the shard key is ```university```) with a unique index on the fields `student_id` and `university`:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

In the preceding example, omitting the ```"university":1``` clause returns an error with the following message:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### TTL indexes

To enable document expiration in a particular collection, you need to create a [time-to-live (TTL) index](../cosmos-db/time-to-live.md). A TTL index is an index on the `_ts` field with an `expireAfterSeconds` value.

Example:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

The preceding command deletes any documents in the ```db.coll``` collection that have not been modified in the last 10 seconds.

> [!NOTE]
> The **_ts** field is specific to Azure Cosmos DB and is not accessible from MongoDB clients. It is a reserved (system) property that contains the time stamp of the document's last modification.

## Track index progress

Version 3.6 of Azure Cosmos DB's API for MongoDB supports the `currentOp()` command to track index progress on a database instance. This command returns a document that contains information about in-progress operations on a database instance. You use the `currentOp` command to track all in-progress operations in native MongoDB. In Azure Cosmos DB's API for MongoDB, this command only supports tracking the index operation.

Here are some examples that show how to use the `currentOp` command to track index progress:

* Get the index progress for a collection:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Get the index progress for all collections in a database:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Get the index progress for all databases and collections in an Azure Cosmos account:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### Examples of index progress output

The index progress details show the percentage of progress for the current index operation. Here's an example that shows the output document format for different stages of index progress:

- An index operation on a "foo" collection and "bar" database that is 60 percent complete will have the following output document. The `Inprog[0].progress.total` field shows 100 as the target completion percentage.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- If an index operation has just started on a "foo" collection and "bar" database, the output document might show 0 percent progress until it reaches a measurable level.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- When the in-progress index operation finishes, the output document shows empty `inprog` operations.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## Background index updates

Regardless of the value specified for the **Background** index property, index updates are always done in the background. Because index updates consume Request Units (RUs) at a lower priority than other database operations, index changes won't result in any downtime for writes, updates, or deletes.

There is no impact to read availability when adding a new index. Queries will only utilize new indexes once the index transformation is complete. During the index transformation, the query engine will continue to use existing indexes, so you'll observe similar read performance during the indexing transformation to what you had observed before initiating the indexing change. When adding new indexes, there is also no risk of incomplete or inconsistent query results.

When removing indexes and immediately running queries the have filters on the dropped indexes, results might be inconsistent and incomplete until the index transformation finishes. If you remove indexes, the query engine does not guarantee consistent or complete results when queries filter on these newly removed indexes. Most developers do not drop indexes and then immediately try to query them so, in practice, this situation is unlikely.

> [!NOTE]
> You can [track index progress](#track-index-progress).

## Migrate collections with indexes

Currently, you can only create unique indexes when the collection contains no documents. Popular MongoDB migration tools try to create the unique indexes after importing the data. To circumvent this issue, you can manually create the corresponding collections and unique indexes instead of allowing the migration tool to try. (You can achieve this behavior for ```mongorestore``` by using the `--noIndexRestore` flag in the command line.)

## Indexing for MongoDB version 3.2

Available indexing features and defaults are different for Azure Cosmos accounts that are compatible with version 3.2 of the MongoDB wire protocol. You can [check your account's version](mongodb-feature-support-36.md#protocol-support). You can upgrade to the 3.6 version by filing a [support request](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

If you're using version 3.2, this section outlines key differences with version 3.6.

### Dropping default indexes (version 3.2)

Unlike the 3.6 version of Azure Cosmos DB's API for MongoDB, version 3.2 indexes every property by default. You can use the following command to drop these default indexes for a collection (```coll```):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

After dropping the default indexes, you can add more indexes as you would in version 3.6.

### Compound indexes (version 3.2)

Compound indexes hold references to multiple fields of a document. If you want to create a compound index, upgrade to version 3.6 by filing a [support request](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### Wildcard indexes (version 3.2)

If you want to create a wildcard index, upgrade to version 3.6 by filing a [support request](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## Next steps

* [Indexing in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md)
* To learn about the relationship between partitioning and indexing, see how to [Query an Azure Cosmos container](how-to-query-container.md) article.
