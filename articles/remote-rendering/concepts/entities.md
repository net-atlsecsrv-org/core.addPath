---
title: Entities
description: Definition of entities in the scope of the Azure Remote Rendering API
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
---

# Entities

An *Entity* represents a movable object in space and is the fundamental building block of remotely rendered content.

## Entity properties

Entities have a transform defined by a position, rotation, and scale. By themselves entities do not have any observable functionality. Instead, behavior is added through components, which are attached to entities. For instance, attaching a [CutPlaneComponent](../overview/features/cut-planes.md)  will create a cut plane at the position of the entity.

The most important aspect of the entity itself is the hierarchy and the resulting hierarchical transform. For example, when multiple entities are attached as children to a shared parent entity, all of these entities can be moved, rotated, and scaled in unison by changing the transform of the parent entity.

An entity is uniquely owned by its parent, meaning that when the parent is destroyed with `Entity.Destroy()`, so are its children and all connected [components](components.md). Thus, removing a model from the scene is accomplished by calling `Destroy` on the root node of a model, returned by `AzureSession.Actions.LoadModelAsync()` or its SAS variant `AzureSession.Actions.LoadModelFromSASAsync()`.

Entities are created when the server loads content or when the user wants to add an object to the scene. For example, if a user wants to add a cut plane to visualize the interior of a mesh, the user can create an entity where the plane should exist and then add the cut plane component to it.

## Query functions

There are two types of query functions on entities: synchronous and asynchronous calls. Synchronous queries can only be used for data that is present on the client and does not involve much computation. Examples are querying for components, relative object transforms, or parent/child relationships. Asynchronous queries are used for data that only resides on the server or involves extra computation that would be too expensive to run on the client. Examples are spatial bounds queries or meta data queries.

### Querying components

To find a component of a specific type, use `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### Querying transforms

Transform queries are synchronous calls on the object. It is important to note that transforms queried through the API are local space transforms, relative to the object's parent. Exceptions are root objects, for which local space and world space are identical.

> [!NOTE]
> There is no dedicated API to query the world space transform of arbitrary objects.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### Querying spatial bounds

Bounds queries are asynchronous calls that operate on a full object hierarchy, using one entity as a root. See the dedicated chapter about [object bounds](object-bounds.md).

### Querying metadata

Metadata is additional data stored on objects, that is ignored by the server. Object metadata is essentially a set of (name, value) pairs, where _value_ can be of numeric, boolean or string type. Metadata can be exported with the model.

Metadata queries are asynchronous calls on a specific entity. The query only returns the metadata of a single entity, not the merged information of a sub graph.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

The query will succeed even if the object does not hold any metadata.

## Next steps

* [Components](components.md)
* [Object bounds](object-bounds.md)
