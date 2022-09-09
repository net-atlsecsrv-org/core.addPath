---
title: Troubleshoot Azure Cosmos DB not found exceptions
description: Learn how to diagnose and fix not found exceptions.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
---

# Diagnose and troubleshoot Azure Cosmos DB not found exceptions
The HTTP status code 404 represents that the resource no longer exists.

## Expected behavior
There are many valid scenarios where an application expects a code 404 and correctly handles the scenario.

## A not found exception was returned for an item that should exist or does exist
Here are the possible reasons for a status code 404 to be returned if the item should exist or does exist.

### Race condition
There are multiple SDK client instances and the read happened before the write.

#### Solution:
1. The default account consistency for Azure Cosmos DB is session consistency. When an item is created or updated, the response returns a session token that can be passed between SDK instances to guarantee that the read request is reading from a replica with that change.
1. Change the [consistency level](consistency-levels-choosing.md) to a [stronger level](consistency-levels-tradeoffs.md).

### Invalid partition key and ID combination
The partition key and ID combination aren't valid.

#### Solution:
Fix the application logic that's causing the incorrect combination. 

### Invalid character in an item ID
An item is inserted into Azure Cosmos DB with an [invalid character](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks) in the item ID.

#### Solution:
Change the ID to a different value that doesn't contain the special characters. If changing the ID isn't an option, you can Base64 encode the ID to escape the special characters.

Items already inserted in the container for the ID can be replaced by using RID values instead of name-based references.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### Time to Live purge
The item had the [Time to Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) property set. The item was purged because the TTL property expired.

#### Solution:
Change the TTL property to prevent the item from being purged.

### Lazy indexing
The [lazy indexing](index-policy.md#indexing-mode) hasn't caught up.

#### Solution:
Wait for the indexing to catch up or change the indexing policy.

### Parent resource deleted
The database or container that the item exists in was deleted.

#### Solution:
1. [Restore](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) the parent resource, or re-create the resources.
1. Create a new resource to replace the deleted resource.

### 7. Container/Collection names are case-sensitive
Container/Collection names are case-sesnsitive in Cosmos DB.

#### Solution:
Make sure to use the exact name while connecting to Cosmos DB.

## Next steps
* [Diagnose and troubleshoot](troubleshoot-dot-net-sdk.md) issues when you use the Azure Cosmos DB .NET SDK.
* Learn about performance guidelines for [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) and [.NET v2](performance-tips.md).