---
title: Encryption scopes for Blob storage
description: Encryption scopes provide the ability to manage encryption at the level of the container or an individual blob. You can use encryption scopes to create secure boundaries between data that resides in the same storage account but belongs to different customers.
services: storage
author: tamram

ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
---

# Encryption scopes for Blob storage

Encryption scopes enable you to manage encryption with a key that is scoped to a container or an individual blob. You can use encryption scopes to create secure boundaries between data that resides in the same storage account but belongs to different customers.

For more information about working with encryption scopes, see [Create and manage encryption scopes](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## How encryption scopes work

By default, a storage account is encrypted with a key that is scoped to the entire storage account. When you define an encryption scope, you specify a key that may be scoped to a container or an individual blob. When the encryption scope is applied to a blob, the blob is encrypted with that key. When the encryption scope is applied to a container, it serves as the default scope for blobs in that container, so that all blobs that are uploaded to that container may be encrypted with the same key. The container can be configured to enforce the default encryption scope for all blobs in the container, or to permit an individual blob to be uploaded to the container with an encryption scope other than the default.

Read operations on a blob that was created with an encryption scope happen transparently, so long as the encryption scope is not disabled.

### Key management

When you define an encryption scope, you can specify whether the scope is protected with a Microsoft-managed key or with a customer-managed key that is stored in Azure Key Vault. Different encryption scopes on the same storage account can use either Microsoft-managed or customer-managed keys. You can also switch the type of key used to protect an encryption scope from a customer-managed key to  a Microsoft-managed key, or vice versa, at any time. For more information about customer-managed keys, see [Customer-managed keys for Azure Storage encryption](../common/customer-managed-keys-overview.md). For more information about Microsoft-managed keys, see [About encryption key management](../common/storage-service-encryption.md#about-encryption-key-management).

If you define an encryption scope with a customer-managed key, then you can choose to update the key version either automatically or manually. If you choose to automatically update the key version, then Azure Storage checks the key vault or managed HSM daily for a new version of the customer-managed key and automatically updates the key to the latest version. For more information about updating the key version for a customer-managed key, see [Update the key version](../common/customer-managed-keys-overview.md#update-the-key-version).

A storage account may have up to 10,000 encryption scopes that are protected with customer-managed keys for which the key version is automatically updated. If your storage account already has 10,000 encryption scopes that are protected with customer-managed keys that are being automatically updated, then the key version must be updated manually for any additional encryption scopes that are protected with customer-managed keys.  

### Encryption scopes for containers and blobs

When you create a container, you can specify a default encryption scope for the blobs that are subsequently uploaded to that container. When you specify a default encryption scope for a container, you can decide how the default encryption scope is enforced:

- You can require that all blobs uploaded to the container use the default encryption scope. In this case, every blob in the container is encrypted with the same key.
- You can permit a client to override the default encryption scope for the container, so that a blob may be uploaded with an encryption scope other than the default scope. In this case, the blobs in the container may be encrypted with different keys.

The following table summarizes the behavior of a blob upload operation, depending on how the default encryption scope is configured for the container:

| The encryption scope defined on the container is… | Uploading a blob with the default encryption scope… | Uploading a blob with an encryption scope other than the default scope… |
|--|--|--|
| A default encryption scope with overrides permitted | Succeeds | Succeeds |
| A default encryption scope with overrides prohibited | Succeeds | Fails |

A default encryption scope must be specified for a container at the time that the container is created.

If no default encryption scope is specified for the container, then you can upload a blob using any encryption scope that you've defined for the storage account. The encryption scope must be specified at the time that the blob is uploaded.

## Disabling an encryption scope

When you disable an encryption scope, any subsequent read or write operations made with the encryption scope will fail with HTTP error code 403 (Forbidden). If you re-enable the encryption scope, read and write operations will proceed normally again.

When an encryption scope is disabled, you are no longer billed for it. Disable any encryption scopes that are not needed to avoid unnecessary charges.

If your encryption scope is protected with a customer-managed key, and you delete the key in the key vault, the data will become inaccessible. Be sure to also disable the encryption scope to avoid being charged for it.

Keep in mind that customer-managed keys are protected by soft delete and purge protection in the key vault, and a deleted key is subject to the behavior defined for by those properties. For more information, see one of the following topics in the Azure Key Vault documentation:

- [How to use soft-delete with PowerShell](../../key-vault/general/key-vault-recovery.md)
- [How to use soft-delete with CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> It is not possible to delete an encryption scope.

## Next steps

- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md)
- [Create and manage encryption scopes](encryption-scope-manage.md)
- [Customer-managed keys for Azure Storage encryption](../common/customer-managed-keys-overview.md)
- [What is Azure Key Vault?](../../key-vault/general/overview.md)