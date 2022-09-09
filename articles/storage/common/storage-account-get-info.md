---
title: Get storage account type and SKU name with .NET
titleSuffix: Azure Storage
description: Learn how to get Azure Storage account type and SKU name using the .NET client library.
services: storage
author: mhopkins-msft

ms.author: mhopkins
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
---

# Get storage account type and SKU name with .NET

This article shows how to get the Azure Storage account type and SKU name for a blob by using the [Azure Storage client library for .NET](/dotnet/api/overview/azure/storage).

## About account type and SKU name

**Account type**: Valid account types include `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage`, and `StorageV2`. [Azure storage account overview](storage-account-overview.md) has more information, including descriptions of the various storage accounts.

**SKU name**: Valid SKU names include `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS`, and `Standard_ZRS`. SKU names are case-sensitive and are string fields in the [SkuName Class](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## Retrieve account information

The following code example retrieves and displays the read-only account properties.

# [.NET v12](#tab/dotnet)

To get the storage account type and SKU name associated with a blob, call the [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) or [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) method.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# [.NET v11](#tab/dotnet11)

To get the storage account type and SKU name associated with a blob, call the [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) or [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) method.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## Next steps

Learn about other operations you can perform on a storage account through the [Azure portal](https://portal.azure.com) and the Azure REST API.

- [Get Account Information operation (REST)](/rest/api/storageservices/get-account-information)
