---
title: MongoDB per-document TTL feature in Azure Cosmos DB
description: Learn how to set time to live value for documents using Azure Cosmos DB's API for MongoDB to automatically purge them from the system after a period of time.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.devlang: javascript
ms.topic: how-to
ms.date: 12/26/2018
---
# Expire data with Azure Cosmos DB's API for MongoDB

Time-to-live (TTL) functionality allows the database to automatically expire data. Azure Cosmos DB's API for MongoDB utilizes Cosmos DB's core TTL capabilities. Two modes are supported: setting a default TTL value on the whole collection, and setting individual TTL values for each document. The logic governing TTL indexes and per-document TTL values in Cosmos DB's API for MongoDB is the [same as in Cosmos DB](../cosmos-db/mongodb-indexing.md).

## TTL indexes
To enable TTL universally on a collection, a ["TTL index" (time-to-live index)](../cosmos-db/mongodb-indexing.md) needs to be created. The TTL index is an index on the _ts field with an "expireAfterSeconds" value.

Example:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

The command in the above example will create an index with TTL functionality. Once the index is created, the database will automatically delete any documents in that collection that have not been modified in the last 10 seconds. 

> [!NOTE]
> **_ts** is a Cosmos DB-specific field and is not accessible from MongoDB clients. It is a reserved (system) property that contains the timestamp of the document's last modification.
>
	
Additionally, a C# example: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## Set time to live value for a document 
Per-document TTL values are also supported. The document(s) must contain a root-level property "ttl" (lower-case), and a TTL index as described above must have been created for that collection. TTL values set on a document will override the collection's TTL value.

The TTL value must be an int32. Alternatively, an int64 that fits in an int32, or a double with no decimal part that fits in an int32. Values for the TTL property that do not conform to these specifications are allowed but not treated as a meaningful document TTL value.

The TTL value for the document is optional; documents without a TTL value can be inserted into the collection.  In this case, the collection's TTL value will be honored. 

The following documents have valid TTL values. Once the documents are inserted, the document TTL values override the collection's TTL values. So, the documents will be removed after 20 seconds. 	

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

The following documents have invalid TTL values. The documents will be inserted, but the document TTL value will not be honored. So, the documents will be removed after 10 seconds because of the collection's TTL value. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## How to activate the per-document TTL feature

The per-document TTL feature can be activated with Azure Cosmos DB's API for MongoDB.

:::image type="content" source="./media/mongodb-ttl/mongodb_portal_ttl.png" alt-text="Screenshot of the Per-document TTL feature activation in Portal":::

## Next steps
* [Expire data in Azure Cosmos DB automatically with time to live](../cosmos-db/time-to-live.md)
* [Indexing your Cosmos database configured with Azure Cosmos DB's API for MongoDB](../cosmos-db/mongodb-indexing.md)
