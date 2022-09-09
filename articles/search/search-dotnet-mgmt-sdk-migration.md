---
title: Upgrade to the Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Upgrade to the Azure Search .NET Management SDK from previous versions. Learn about new features and the code changes necessary for migration.

manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
---

# Upgrading versions of the Azure Search .NET Management SDK

This article explains how to migrate to successive versions of the Azure Search .NET Management SDK, used to provision or deprovision search services, adjust capacity, and manage API keys.

Management SDKs target a specific version of the Management REST API. For more information about concepts and operations, see [Search Management (REST)](/rest/api/searchmanagement/).

## Versions

| SDK version | Corresponding REST API version | Feature addition or behavior change |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-version=2020-30-20 | Adds endpoint security (IP firewalls and integration with [Azure Private Link](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version=2019-10-01 | Usability improvements. Breaking change on [List Query Keys](/rest/api/searchmanagement/querykeys/listbysearchservice) (GET is discontinued). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version=2015-08-19  | First version |

## How to upgrade

1. Update your NuGet reference for `Microsoft.Azure.Management.Search` using either the NuGet Package Manager Console or by right-clicking on your project references and selecting "Manage NuGet Packages..." in Visual Studio.

1. Once NuGet has downloaded the new packages and their dependencies, rebuild your project. Depending on how your code is structured, it may rebuild successfully, in which case you are done.

1. If your build fails, it could be because you've implemented some of the SDK interfaces (for example, for the purposes of unit testing), which have changed. To resolve this, you'll need to implement newer methods such as `BeginCreateOrUpdateWithHttpMessagesAsync`.

1. After fixing any build errors, you can make changes to your application to take advantage of new functionality. 

## Upgrade to 3.0

Version 3.0 adds private endpoint protection by restricting access to IP ranges, and by optionally integrating with Azure Private Link for search services that should not be visible on the public internet.

### New APIs

| API | Category| Details |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | IP firewall | Restrict access to a service endpoint to a list of allowed IP addresses. See [Configure IP firewall](service-configure-firewall.md) for concepts and portal instructions. |
| [Shared Private Link Resource](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | Create a shared private link resource to be used by a search service.  |
| [Private Endpoint Connections](/rest/api/searchmanagement/privateendpointconnections) | Private Link | Establish and manage connections to a search service through private endpoint. See [Create a private endpoint](service-create-private-endpoint.md) for concepts and portal instructions.|
| [Private Link Resources](/rest/api/searchmanagement/privatelinkresources/) | Private Link | For a search service that has a private endpoint connection, get a list of all services used in the same virtual network. If your search solution includes indexers that pull from Azure data sources (Azure Storage, Cosmos DB, Azure SQL), or uses Cognitive Services or Key Vault, then all of those resources should have endpoints in the virtual network, and this API should return a list. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | This is a property on Create or Update Service requests. When disabled, private link is the only access modality. |

### Breaking changes

You can no longer use GET on a [List Query Keys](/rest/api/searchmanagement/querykeys/listbysearchservice) request. In previous releases you could use either GET or POST, in this release and in all releases moving forward, only POST is supported. 

## Upgrade to 2.0

Version 2 of the Azure Search .NET Management SDK is a minor upgrade, so changing your code should require only minimal effort.The changes to the SDK are strictly client-side changes to improve the usability of the SDK itself. These changes include the following:

* `Services.CreateOrUpdate` and its asynchronous versions now automatically poll the provisioning `SearchService` and do not return until service provisioning is complete. This saves you from having to write such polling code yourself.

* If you still want to poll service provisioning manually, you can use the new `Services.BeginCreateOrUpdate` method or one of its asynchronous versions.

* New methods `Services.Update` and its asynchronous versions have been added to the SDK. These methods use HTTP PATCH to support incremental updating of a service. For example, you can now scale a service by passing a `SearchService` instance to these methods that contains only the desired `partitionCount` and `replicaCount` properties. The old way of calling `Services.Get`, modifying the returned `SearchService`, and passing it to `Services.CreateOrUpdate` is still supported, but is no longer necessary. 

## Next steps

If you encounter problems, the best forum for posting questions is [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). If you find a bug, you can file an issue in the [Azure .NET SDK GitHub repository](https://github.com/Azure/azure-sdk-for-net/issues). Make sure to label your issue title with "[search]".