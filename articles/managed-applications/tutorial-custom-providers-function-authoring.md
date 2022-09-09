---
title: Author a RESTful endpoint for custom providers
description: This tutorial shows how to author a RESTful endpoint for custom providers. It details how to handle requests and responses for the supported RESTful HTTP methods.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
---

# Author a RESTful endpoint for custom providers

A custom provider is a contract between Azure and an endpoint. With custom providers, you can customize workflows on Azure. This tutorial shows how to author a custom provider RESTful endpoint. If you're unfamiliar with Azure Custom Providers, see [the overview on custom resource providers](./custom-providers-overview.md).

> [!NOTE]
> This tutorial builds on the tutorial [Set up Azure Functions for Azure Custom Providers](./tutorial-custom-providers-function-setup.md). Some of the steps in this tutorial work only if an Azure function app has been set up to work with custom providers.

## Work with custom actions and custom resources

In this tutorial, you update the function app to work as a RESTful endpoint for your custom provider. Resources and actions in Azure are modeled after the following basic RESTful specification:

- **PUT**: Create a new resource
- **GET (instance)**: Retrieve an existing resource
- **DELETE**: Remove an existing resource
- **POST**: Trigger an action
- **GET (collection)**: List all existing resources

 For this tutorial, you use Azure Table storage. But any database or storage service can work.

## Partition custom resources in storage

Because you're creating a RESTful service, you need to store the created resources. For Azure Table storage, you need to generate partition and row keys for your data. For custom providers, data should be partitioned to the custom provider. When an incoming request is sent to the custom provider, the custom provider adds the `x-ms-customproviders-requestpath` header to outgoing requests to the endpoint.

The following example shows an `x-ms-customproviders-requestpath` header for a custom resource:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Based on the example's `x-ms-customproviders-requestpath` header, you can create the *partitionKey* and *rowKey* parameters for your storage as shown in the following table:

Parameter | Template | Description
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | The *partitionKey* parameter specifies how the data is partitioned. Usually the data is partitioned by the custom provider instance.
*rowKey* | `{myResourceType}:{myResourceName}` | The *rowKey* parameter specifies the individual identifier for the data. Usually the identifier is the name of the resource.

You also need to create a new class to model your custom resource. In this tutorial, you add the following **CustomResource** class to your function app:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** is a simple, generic class that accepts any input data. It's based on **TableEntity**, which is used to store data. The **CustomResource** class inherits two properties from **TableEntity**: **partitionKey** and **rowKey**.

## Support custom provider RESTful methods

> [!NOTE]
> If you aren't copying the code directly from this tutorial, the response content must be valid JSON that sets the `Content-Type` header to `application/json`.

Now that you've set up data partitioning, create the basic CRUD and trigger methods for custom resources and custom actions. Because custom providers act as proxies, the RESTful endpoint must model and handle the request and response. The following code snippets show how to handle the basic RESTful operations.

### Trigger a custom action

For custom providers, a custom action is triggered through POST requests. A custom action can optionally accept a request body that contains a set of input parameters. The action then returns a response that signals the result of the action and whether it succeeded or failed.

Add the following **TriggerCustomAction** method to your function app:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

The **TriggerCustomAction** method accepts an incoming request and simply echoes back the response with a status code.

### Create a custom resource

For custom providers, a custom resource is created through PUT requests. The custom provider accepts a JSON request body, which contains a set of properties for the custom resource. Resources in Azure follow a RESTful model. You can use the same request URL to create, retrieve, or delete a resource.

Add the following **CreateCustomResource** method to create new resources:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

The **CreateCustomResource** method updates the incoming request to include the Azure-specific fields **id**, **name**, and **type**. These fields are top-level properties used by services across Azure. They let the custom provider interoperate with other services like Azure Policy, Azure Resource Manager Templates, and Azure Activity Log.

Property | Example | Description
---|---|---
**name** | {myCustomResourceName} | The name of the custom resource
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | The resource-type namespace
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | The resource ID

In addition to adding the properties, you also saved the JSON document to Azure Table storage.

### Retrieve a custom resource

For custom providers, a custom resource is retrieved through GET requests. A custom provider *doesn't* accept a JSON request body. For GET requests, the endpoint uses the `x-ms-customproviders-requestpath` header to return the already created resource.

Add the following **RetrieveCustomResource** method to retrieve existing resources:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

In Azure, resources follow a RESTful model. The request URL that creates a resource also returns the resource if a GET request is performed.

### Remove a custom resource

For custom providers, a custom resource is removed through DELETE requests. A custom provider *doesn't* accept a JSON request body. For a DELETE request, the endpoint uses the `x-ms-customproviders-requestpath` header to delete the already created resource.

Add the following **RemoveCustomResource** method to remove existing resources:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

In Azure, resources follow a RESTful model. The request URL that creates a resource also deletes the resource if a DELETE request is performed.

### List all custom resources

For custom providers, you can enumerate a list of existing custom resources by using collection GET requests. A custom provider *doesn't* accept a JSON request body. For a collection of GET requests, the endpoint uses the `x-ms-customproviders-requestpath` header to enumerate the already created resources.

Add the following **EnumerateAllCustomResources** method to enumerate the existing resources:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> The RowKey QueryComparisons.GreaterThan and QueryComparisons.LessThan is Azure Table storage syntax to perform a "startswith" query for strings.

To list all existing resources, generate an Azure Table storage query that ensures the resources exist under your custom provider partition. The query then checks that the row key starts with the same `{myResourceType}` value.

## Integrate RESTful operations

After all the RESTful methods are added to the function app, update the main **Run** method that calls the functions to handle the different REST requests:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

The updated **Run** method now includes the *tableStorage* input binding that you added for Azure Table storage. The first part of the method reads the `x-ms-customproviders-requestpath` header and uses the `Microsoft.Azure.Management.ResourceManager.Fluent` library to parse the value as a resource ID. The `x-ms-customproviders-requestpath` header is sent by the custom provider and specifies the path of the incoming request.

By using the parsed resource ID, you can generate the **partitionKey** and **rowKey** values for the data to look up or to store custom resources.

After you add the methods and classes, you need to update the **using** methods for the function app. Add the following code to the top of the C# file:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

If you get lost at any point of this tutorial, you can find the complete code sample in the [custom provider C# RESTful endpoint reference](./reference-custom-providers-csharp-endpoint.md). After you've finished the function app, save the function app URL. It can be used to trigger the function app in later tutorials.

## Next steps

In this article, you authored a RESTful endpoint to work with an Azure Custom Provider endpoint. To learn how to create a custom provider, go to the article [Tutorial: Creating a custom provider](./tutorial-custom-providers-create.md).
