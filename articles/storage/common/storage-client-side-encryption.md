---
title: Client-Side Encryption with .NET for Microsoft Azure Storage | Microsoft Docs
description: The Azure Storage Client Library for .NET supports client-side encryption and integration with Azure Key Vault for maximum security for your Azure Storage applications.
services: storage
author: tamram

ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
---

# Client-Side Encryption and Azure Key Vault for Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## Overview
The [Azure Storage Client Library for .NET](/dotnet/api/overview/azure/storage) supports encrypting data within client applications before uploading to Azure Storage, and decrypting data while downloading to the client. The library also supports integration with [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) for storage account key management.

For a step-by-step tutorial that leads you through the process of encrypting blobs using client-side encryption and Azure Key Vault, see [Encrypt and decrypt blobs in Microsoft Azure Storage using Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

For client-side encryption with Java, see [Client-Side Encryption with Java for Microsoft Azure Storage](storage-client-side-encryption-java.md).

## Encryption and decryption via the envelope technique
The processes of encryption and decryption follow the envelope technique.

### Encryption via the envelope technique
Encryption via the envelope technique works in the following way:

1. The Azure storage client library generates a content encryption key (CEK), which is a one-time-use symmetric key.
2. User data is encrypted using this CEK.
3. The CEK is then wrapped (encrypted) using the key encryption key (KEK). The KEK is identified by a key identifier and can be an asymmetric key pair or a symmetric key and can be managed locally or stored in Azure Key Vaults.
   
    The storage client library itself never has access to KEK. The library invokes the key wrapping algorithm that is provided by Key Vault. Users can choose to use custom providers for key wrapping/unwrapping if desired.

4. The encrypted data is then uploaded to the Azure Storage service. The wrapped key along with some additional encryption metadata is either stored as metadata (on a blob) or interpolated with the encrypted data (queue messages and table entities).

### Decryption via the envelope technique
Decryption via the envelope technique works in the following way:

1. The client library assumes that the user is managing the key encryption key (KEK) either locally or in Azure Key Vaults. The user does not need to know the specific key that was used for encryption. Instead, a key resolver which resolves different key identifiers to keys can be set up and used.
2. The client library downloads the encrypted data along with any encryption material that is stored on the service.
3. The wrapped content encryption key (CEK) is then unwrapped (decrypted) using the key encryption key (KEK). Here again, the client library does not have access to KEK. It simply invokes the custom or Key Vault provider's unwrapping algorithm.
4. The content encryption key (CEK) is then used to decrypt the encrypted user data.

## Encryption Mechanism
The storage client library uses [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) in order to encrypt user data. Specifically, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mode with AES. Each service works somewhat differently, so we will discuss each of them here.

### Blobs
The client library currently supports encryption of whole blobs only. For downloads, both complete and range downloads are supported.

During encryption, the client library will generate a random Initialization Vector (IV) of 16 bytes, together with a random content encryption key (CEK) of 32 bytes, and perform envelope encryption of the blob data using this information. The wrapped CEK and some additional encryption metadata are then stored as blob metadata along with the encrypted blob on the service.

> [!WARNING]
> If you are editing or uploading your own metadata for the blob, you need to ensure that this metadata is preserved. If you upload new metadata without this metadata, the wrapped CEK, IV, and other metadata will be lost and the blob content will never be retrievable again.
> 
> 

When downloading an entire blob, the wrapped CEK is unwrapped and used together with the IV (stored as blob metadata in this case) to return the decrypted data to the users.

Downloading an arbitrary range in the encrypted blob involves adjusting the range provided by users in order to get a small amount of additional data that can be used to successfully decrypt the requested range.

All blob types (block blobs, page blobs, and append blobs) can be encrypted/decrypted using this scheme.

### Queues
Since queue messages can be of any format, the client library defines a custom format that includes the Initialization Vector (IV) and the encrypted content encryption key (CEK) in the message text.

During encryption, the client library generates a random IV of 16 bytes along with a random CEK of 32 bytes and performs envelope encryption of the queue message text using this information. The wrapped CEK and some additional encryption metadata are then added to the encrypted queue message. This modified message (shown below) is stored on the service.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

During decryption, the wrapped key is extracted from the queue message and unwrapped. The IV is also extracted from the queue message and used along with the unwrapped key to decrypt the queue message data. Note that the encryption metadata is small (under 500 bytes), so while it does count toward the 64KB limit for a queue message, the impact should be manageable. Note that the encrypted message will be base64 encoded, as shown in the above snippet, which will also expand the size of the message being sent.

### Tables
> [!NOTE]
> The Table service is supported in the Azure Storage client library through version 9.x only.
> 
> 

The client library supports encryption of entity properties for insert and replace operations.

> [!NOTE]
> Merge is not currently supported. Since a subset of properties may have been encrypted previously using a different key, simply merging the new properties and updating the metadata will result in data loss. Merging either requires making extra service calls to read the pre-existing entity from the service, or using a new key per property, both of which are not suitable for performance reasons.
> 
> 

Table data encryption works as follows:  

1. Users specify the properties to be encrypted.
2. The client library generates a random Initialization Vector (IV) of 16 bytes along with a random content encryption key (CEK) of 32 bytes for every entity, and performs envelope encryption on the individual properties to be encrypted by deriving a new IV per property. The encrypted property is stored as binary data.
3. The wrapped CEK and some additional encryption metadata are then stored as two additional reserved properties. The first reserved property (_ClientEncryptionMetadata1) is a string property that holds the information about IV, version, and wrapped key. The second reserved property (_ClientEncryptionMetadata2) is a binary property that holds the information about the properties that are encrypted. The information in this second property (_ClientEncryptionMetadata2) is itself encrypted.
4. Due to these additional reserved properties required for encryption, users may now have only 250 custom properties instead of 252. The total size of the entity must be less than 1 MB.

Note that only string properties can be encrypted. If other types of properties are to be encrypted, they must be converted to strings. The encrypted strings are stored on the service as binary properties, and they are converted back to strings after decryption.

For tables, in addition to the encryption policy, users must specify the properties to be encrypted. This can be done by either specifying an [EncryptProperty] attribute (for POCO entities that derive from TableEntity) or an encryption resolver in request options. An encryption resolver is a delegate that takes a partition key, row key, and property name and returns a Boolean that indicates whether that property should be encrypted. During encryption, the client library will use this information to decide whether a property should be encrypted while writing to the wire. The delegate also provides for the possibility of logic around how properties are encrypted. (For example, if X, then encrypt property A; otherwise encrypt properties A and B.) Note that it is not necessary to provide this information while reading or querying entities.

### Batch Operations
In batch operations, the same KEK will be used across all the rows in that batch operation because the client library only allows one options object (and hence one policy/KEK) per batch operation. However, the client library will internally generate a new random IV and random CEK per row in the batch. Users can also choose to encrypt different properties for every operation in the batch by defining this behavior in the encryption resolver.

### Queries
> [!NOTE]
> Because the entities are encrypted, you cannot run queries that filter on an encrypted property.  If you try, results will be incorrect, because the service would be trying to compare encrypted data with unencrypted data.
> 
> 
> To perform query operations, you must specify a key resolver that is able to resolve all the keys in the result set. If an entity contained in the query result cannot be resolved to a provider, the client library will throw an error. For any query that performs server-side projections, the client library will add the special encryption metadata properties (_ClientEncryptionMetadata1 and _ClientEncryptionMetadata2) by default to the selected columns.

## Azure Key Vault
Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. By using Azure Key Vault, users can encrypt keys and secrets (such as authentication keys, storage account keys, data encryption keys, .PFX files, and passwords) by using keys that are protected by hardware security modules (HSMs). For more information, see [What is Azure Key Vault?](../../key-vault/general/overview.md).

The storage client library uses the Key Vault interfaces in the core library in order to provide a common framework across Azure for managing keys. Users can leverage Key Vault libraries for all the additional benefits they provide, such as useful functionality around simple and seamless Symmetric/RSA local and cloud key providers as well as help with aggregation and caching.

### Interface and dependencies

# [.NET v12](#tab/dotnet)

There are two necessary packages for Key Vault integration:

* Azure.Core contains the `IKeyEncryptionKey` and `IKeyEncryptionKeyResolver` interfaces. The storage client library for .NET already defines it as a dependency.
* Azure.Security.KeyVault.Keys (v4.x) contains the Key Vault REST client, as well as cryptographic clients used with client-side encryption.

# [.NET v11](#tab/dotnet11)

There are three Key Vault packages:

* Microsoft.Azure.KeyVault.Core contains the IKey and IKeyResolver. It is a small package with no dependencies. The storage client library for .NET defines it as a dependency.
* Microsoft.Azure.KeyVault (v3.x) contains the Key Vault REST client.
* Microsoft.Azure.KeyVault.Extensions (v3.x) contains extension code that includes implementations of cryptographic algorithms and an RSAKey and a SymmetricKey. It depends on the Core and KeyVault namespaces and provides functionality to define an aggregate resolver (when users want to use multiple key providers) and a caching key resolver. Although the storage client library does not directly depend on this package, if users wish to use Azure Key Vault to store their keys or to use the Key Vault extensions to consume the local and cloud cryptographic providers, they will need this package.

More information regarding Key Vault usage in v11 can be found in the [v11 encryption code samples](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

---

Key Vault is designed for high-value master keys, and throttling limits per Key Vault are designed with this in mind. When performing client-side encryption with Key Vault, the preferred model is to use symmetric master keys stored as secrets in Key Vault and cached locally. Users must do the following:

1. Create a secret offline and upload it to Key Vault.
2. Use the secret's base identifier as a parameter to resolve the current version of the secret for encryption and cache this information locally. Use CachingKeyResolver for caching; users are not expected to implement their own caching logic.
3. Use the caching resolver as an input while creating the encryption policy.

## Best practices
Encryption support is available only in the storage client library for .NET. Windows Phone and Windows Runtime do not currently support encryption.

> [!IMPORTANT]
> Be aware of these important points when using client-side encryption:
> 
> * When reading from or writing to an encrypted blob, use whole blob upload commands and range/whole blob download commands. Avoid writing to an encrypted blob using protocol operations such as Put Block, Put Block List, Write Pages, Clear Pages, or Append Block; otherwise you may corrupt the encrypted blob and make it unreadable.
> * For tables, a similar constraint exists. Be careful to not update encrypted properties without updating the encryption metadata.
> * If you set metadata on the encrypted blob, you may overwrite the encryption-related metadata required for decryption, since setting metadata is not additive. This is also true for snapshots; avoid specifying metadata while creating a snapshot of an encrypted blob. If metadata must be set, be sure to call the **FetchAttributes** method first to get the current encryption metadata, and avoid concurrent writes while metadata is being set.
> * Enable the **RequireEncryption** property in the default request options for users that should work only with encrypted data. See below for more info.
>
>

## Client API / Interface
Users can provide only a key, only a resolver, or both. Keys are identified using a key identifier and provides the logic for wrapping/unwrapping. Resolvers are used to resolve a key during the decryption process. It defines a resolve method that returns a key given a key identifier. This provides users the ability to choose between multiple keys that are managed in multiple locations.

* For encryption, the key is used always and the absence of a key will result in an error.
* For decryption:
  * If the key is specified and its identifier matches the required key identifier, that key is used for decryption. Otherwise, the resolver is attempted. If there is no resolver for this attempt, an error is thrown.
  * The key resolver is invoked if specified to get the key. If the resolver is specified but does not have a mapping for the key identifier, an error is thrown.

### RequireEncryption mode (v11 only)
Users can optionally enable a mode of operation where all uploads and downloads must be encrypted. In this mode, attempts to upload data without an encryption policy or download data that is not encrypted on the service will fail on the client. The **RequireEncryption** property of the request options object controls this behavior. If your application will encrypt all objects stored in Azure Storage, then you can set the **RequireEncryption** property on the default request options for the service client object. For example, set **CloudBlobClient.DefaultRequestOptions.RequireEncryption** to **true** to require encryption for all blob operations performed through that client object.


### Blob service encryption


# [.NET v12](#tab/dotnet)
Create a **ClientSideEncryptionOptions** object and set it on client creation with **SpecializedBlobClientOptions**. You cannot set encryption options on a per-API basis. Everything else will be handled by the client library internally.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("myContainer").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

A **BlobServiceClient** is not necessary to apply encryption options. They can also be passed into **BlobContainerClient**/**BlobClient** constructors that accept **BlobClientOptions** objects.

If a desired **BlobClient** object already exists but without client-side encryption options, an extension method exists to create a copy of that object with the given **ClientSideEncryptionOptions**. This extension method avoids the overhead of constructing a new **BlobClient** object from scratch.

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# [.NET v11](#tab/dotnet11)
Create a **BlobEncryptionPolicy** object and set it in the request options (per API or at a client level by using **DefaultRequestOptions**). Everything else will be handled by the client library internally.

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### Queue service encryption
# [.NET v12](#tab/dotnet)
Create a **ClientSideEncryptionOptions** object and set it on client creation with **SpecializedQueueClientOptions**. You cannot set encryption options on a per-API basis. Everything else will be handled by the client library internally.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

A **QueueServiceClient** is not necessary to apply encryption options. They can also be passed into **QueueClient** constructors that accept **QueueClientOptions** objects.

If a desired **QueueClient** object already exists but without client-side encryption options, an extension method exists to create a copy of that object with the given **ClientSideEncryptionOptions**. This extension method avoids the overhead of constructing a new **QueueClient** object from scratch.

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

Some users may have queues where not all received messages can be successfully decrypted and the key or resolver must throw. The final line of the above example will throw in this case, and none of the received messages will be accessible. In these scenarios, the sub-class **QueueClientSideEncryptionOptions** can be used to provide encryption options to clients. It exposes an event **DecryptionFailed** that will trigger whenever a queue message fails to be decrypted, so long as at least one invocation has been added to the event. Individually failed messages can be handled this way, and they will be filtered out of the final **QueueMessage[]** returned by **ReceiveMessages**.

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# [.NET v11](#tab/dotnet11)
Create a **QueueEncryptionPolicy** object and set it in the request options (per API or at a client level by using **DefaultRequestOptions**). Everything else will be handled by the client library internally.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### Table service encryption (v11 only)
In addition to creating an encryption policy and setting it on request options, you must either specify an **EncryptionResolver** in **TableRequestOptions**, or set the [EncryptProperty] attribute on the entity.

#### Using the resolver

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### Using attributes
As mentioned above, if the entity implements TableEntity, then the properties can be decorated with the [EncryptProperty] attribute instead of specifying the **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## Encryption and performance
Note that encrypting your storage data results in additional performance overhead. The content key and IV must be generated, the content itself must be encrypted, and additional meta-data must be formatted and uploaded. This overhead will vary depending on the quantity of data being encrypted. We recommend that customers always test their applications for performance during development.

## Next steps
* [Tutorial: Encrypt and decrypt blobs in Microsoft Azure Storage using Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Download the [Azure Storage Client Library for .NET NuGet package](https://www.nuget.org/packages/WindowsAzure.Storage)
* Download the Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/), and [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) packages  
* Visit the [Azure Key Vault Documentation](../../key-vault/general/overview.md)
