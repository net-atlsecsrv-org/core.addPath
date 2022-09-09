---
title: Getting Started with Azure storage using Visual Studio (WebJob projects)
description: How to get started using Azure Table storage in an Azure WebJobs project in Visual Studio after connecting to a storage account using Visual Studio connected services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
---
# Getting Started with Azure Storage (Azure WebJob Projects)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## Overview
This article provides C# code samples that show how to use the Azure WebJobs SDK version 1.x with the Azure table storage service. The code samples use the [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x.

The Azure Table storage service enables you to store large amounts of structured data. The service is a NoSQL datastore that accepts authenticated calls from inside and outside the Azure cloud. Azure tables are ideal for storing structured, non-relational data.  See [Get started with Azure Table storage using .NET](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) for more information.

Some of the code snippets show the **Table** attribute used in functions that are called manually, that is, not by using one of the trigger attributes.

## How to add entities to a table

To add entities to a table, use the **Table** attribute with an **ICollector\<T>** or **IAsyncCollector\<T>** parameter where **T** specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table.

The following code sample adds **Person** entities to a table named *Ingress*.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Typically the type you use with **ICollector** derives from **TableEntity** or implements **ITableEntity**, but it doesn't have to. Either of the following **Person** classes work with the code shown in the preceding **Ingress** method.

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

If you want to work directly with the Azure storage API, you can add a **CloudStorageAccount** parameter to the method signature.

## Real-time monitoring

Because data ingress functions often process large volumes of data, the WebJobs SDK dashboard provides real-time monitoring data. The **Invocation Log** section tells you if the function is still running.

![Ingress function running](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

The **Invocation Details** page reports the function's progress (number of entities written) while it's running and gives you an opportunity to abort it.

![Ingress function running](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

When the function finishes, the **Invocation Details** page reports the number of rows written.

![Ingress function finished](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## How to read multiple entities from a table

To read a table, use the **Table** attribute with an **IQueryable\<T>** parameter where type **T** derives from **TableEntity** or implements **ITableEntity**.

The following code sample reads and logs all rows from the **Ingress** table:

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### How to read a single entity from a table

There is a **Table** attribute constructor with two additional parameters that let you specify the partition key and row key when you want to bind to a single table entity.

The following code sample reads a table row for a **Person** entity based on partition key and row key values received in a queue message:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

The **Person** class in this example does not have to implement **ITableEntity**.

## How to use the .NET Storage API directly to work with a table

You can also use the **Table** attribute with a **CloudTable** object for more flexibility in working with a table.

The following code sample uses a **CloudTable** object to add a single entity to the *Ingress* table.

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

For more information about how to use the **CloudTable** object, see [Get started with Azure Table storage using .NET](../storage/storage-dotnet-how-to-use-tables.md).

## Related topics covered by the queues how-to article

For information about how to handle table processing triggered by a queue message, or for WebJobs SDK scenarios not specific to table processing, see [Getting started with Azure Queue storage and Visual Studio connected services (WebJob Projects)](../storage/vs-storage-webjobs-getting-started-queues.md).

## Next steps

This article has provided code samples that show how to handle common scenarios for working with Azure tables. For more information about how to use Azure WebJobs and the WebJobs SDK, see [Azure WebJobs documentation resources](https://go.microsoft.com/fwlink/?linkid=390226).
