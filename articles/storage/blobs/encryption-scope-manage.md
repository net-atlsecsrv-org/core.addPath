---
title: Create and manage encryption scopes (preview)
description: Learn how to create an encryption scope to isolate blob data at the container or blob level.
services: storage
author: tamram

ms.service: storage
ms.date: 08/25/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
---

# Create and manage encryption scopes (preview)

Encryption scopes (preview) enable you to manage encryption at the level of an individual blob or container. An encryption scope isolates blob data in a secure enclave within a storage account. You can use encryption scopes to create secure boundaries between data that resides in the same storage account but belongs to different customers. For more information about encryption scopes, see [Encryption scopes for Blob storage (preview)](../common/storage-service-encryption.md#encryption-scopes-for-blob-storage-preview).

This article shows how to create an encryption scope. It also shows how to specify an encryption scope when you create a blob or container.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## Create an encryption scope

You can create encryption scopes with a Microsoft-managed key or with a customer-managed key that's stored in Azure Key Vault. To create an encryption scope with a customer-managed key, you must first create an Azure key vault and add the key you intend to use for the scope. The key vault must have both the **Soft Delete** and **Purge Protection** properties enabled and must be in the same region as the storage account. For more information, see [Use customer-managed keys with Azure Key Vault to manage Azure Storage encryption](../common/encryption-customer-managed-keys.md).

An encryption scope is automatically enabled when you create it. After you create the encryption scope, you can specify it when you create a blob. You can also specify a default encryption scope when you create a container, which automatically applies to all blobs in the container.

# [Portal](#tab/portal)

To create an encryption scope in the Azure portal, follow these steps:

1. Navigate to your storage account in the Azure portal.
1. Select the **Encryption** setting.
1. Select the **Encryption Scopes** tab.
1. Click the **Add** button to add a new encryption scope.
1. In the Create **Encryption Scope** pane, enter a name for the new scope.
1. Select the type of encryption, either **Microsoft-managed keys** or **Customer-managed keys**.
    - If you selected **Microsoft-managed keys**, click **Create** to create the encryption scope.
    - If you selected **Customer-managed keys**, specify a key vault, key, and key version to use for this encryption scope, as shown in the following image.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Screenshot showing how to create encryption scope in Azure portal":::

For more information on configuring customer-managed keys with Azure Key Vault for Azure Storage encryption, see [Configure customer-managed keys with Azure Key Vault by using the Azure portal](../common/storage-encryption-keys-portal.md).

# [PowerShell](#tab/powershell)

To create an encryption scope with PowerShell, first install the Az.Storage preview module version. Using the latest preview version is recommended, but encryption scopes are supported in version 1.13.4-preview and later. Remove any other versions of the Az.Storage module.

The following command installs Az.Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) module:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### Create an encryption scope protected by Microsoft-managed keys

To create a new encryption scope that is protected by Microsoft-managed keys, call the **New-AzStorageEncryptionScope** command with the `-StorageEncryption` parameter.

Remember to replace the placeholder values in the example with your own values:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### Create an encryption scope protected by customer-managed keys

To create a new encryption scope that is protected by customer-managed keys with Azure Key Vault, first configure customer-managed keys for the storage account. You must assign a managed identity to the storage account and then use the managed identity to configure the access policy for the key vault so that the storage account has permissions to access it. For more information, see [Configure customer-managed keys with Azure Key Vault by using PowerShell](../common/storage-encryption-keys-powershell.md).

To configure customer-managed keys for use with an encryption scope, both the **Soft Delete** and **Purge Protection** properties must be enabled on the key vault. The key vault must be in the same region as the storage account. For more information, see [Use customer-managed keys with Azure Key Vault to manage Azure Storage encryption](../common/encryption-customer-managed-keys.md).

Remember to replace the placeholder values in the example with your own values:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Next, call the **New-AzStorageEncryptionScope** command with the `-KeyvaultEncryption` parameter, and specify the key URI. Be sure to include the key version on the key URI. Remember to replace the placeholder values in the example with your own values:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# [Azure CLI](#tab/cli)

To create an encryption scope with Azure CLI, first install Azure CLI version 2.4.0 or later.

### Create an encryption scope protected by Microsoft-managed keys

To create a new encryption scope that is protected by Microsoft-managed keys, call the [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) command, specifying the `--key-source` parameter as `Microsoft.Storage`. Remember to replace the placeholder values with your own values:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### Create an encryption scope protected by customer-managed keys

To create a new encryption scope that is protected by Microsoft-managed keys, call the [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) command, specifying the `--key-source` parameter as `Microsoft.Storage`. Remember to replace the placeholder values with your own values:

To create a new encryption scope that is protected by customer-managed keys with Azure Key Vault, first configure customer-managed keys for the storage account. You must assign a managed identity to the storage account and then use the managed identity to configure the access policy for the key vault so that the storage account has permissions to access it. For more information, see [Configure customer-managed keys with Azure Key Vault by using Azure CLI](../common/storage-encryption-keys-cli.md).

To configure customer-managed keys for use with an encryption scope, both the **Soft Delete** and **Purge Protection** properties must be enabled on the key vault. The key vault must be in the same region as the storage account. For more information, see [Use customer-managed keys with Azure Key Vault to manage Azure Storage encryption](../common/encryption-customer-managed-keys.md).

Remember to replace the placeholder values in the example with your own values:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Next, call the **az storage account encryption-scope create** command with the `--key-uri` parameter, and specify the key URI. Be sure to include the key version on the key URI. Remember to replace the placeholder values in the example with your own values:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## List encryption scopes for storage account

# [Portal](#tab/portal)

To view the encryption scopes for a storage account in the Azure portal, navigate to the **Encryption Scopes** setting for the storage account. From this pane, you can enable or disable an encryption scope or change the key for an encryption scope.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Screenshot showing list of encryption scopes in Azure portal":::

# [PowerShell](#tab/powershell)

To list the encryption scopes available for a storage account with PowerShell, call the Get-AzStorageEncryptionScope command. Remember to replace the placeholder values in the example with your own values:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

To list all encryption scopes in a resource group by storage account, use the pipeline syntax as follows:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# [Azure CLI](#tab/cli)

To list the encryption scopes available for a storage account with Azure CLI, call the [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) command. Remember to replace the placeholder values in the example with your own values:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## Create a container with a default encryption scope

# [Portal](#tab/portal)

To create a container with a default encryption scope in the Azure portal, first create the encryption scope as described in [Create an encryption scope](#create-an-encryption-scope). Next, follow these steps to create the container:

1. Navigate to the list of containers in your storage account, and select the **Add** button to create a new container.
1. Expand the **Advanced** settings in the **New Container** pane.
1. In the **Encryption scope** drop-down, select the default encryption scope for the container.
1. To require that all blobs in the container use the default encryption scope, select the checkbox to **Use this encryption scope for all blobs in the container**. If this checkbox is selected, then an individual blob in the container cannot override the default encryption scope.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Screenshot showing container with default encryption scope":::

# [PowerShell](#tab/powershell)

To create a container with a default encryption scope with PowerShell, call the [New-AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) command, specifying the scope for the `-DefaultEncryptionScope` parameter. The **New-AzRmStorageContainer** command creates a container by using the Azure Storage resource provider, which enables configuration of encryption scopes and other resource management operations.

An individual blob can be created with its own encryption scope, unless the container is configured to require that all blobs use its default scope. To force all blobs in a container to use the container's default scope, set the `-PreventEncryptionScopeOverride` parameter to `true`.

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# [Azure CLI](#tab/cli)

To create a container with a default encryption scope with Azure CLI, call the [az storage container create](/cli/azure/storage/container#az-storage-container-create) command, specifying the scope for the `--default-encryption-scope` parameter. An individual blob can be created with its own encryption scope, unless the container is configured to require that all blobs use its default scope. To force all blobs in a container to use the container's default scope, set the `--prevent-encryption-scope-override` parameter to `true`.

The following example uses your Azure AD account to authorize the operation to create the container. You can also use the account access key. For more information, see [Authorize access to blob or queue data with Azure CLI](../common/authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

If a client attempts to specify a scope when uploading a blob to a container that has a default encryption scope and the container is configured to prevent blobs from overriding the default scope, then the operation fails with a message indicating that the request is forbidden by the container encryption policy.

## Upload a blob with an encryption scope

When you upload a blob, you can specify an encryption scope for that blob, or use the default encryption scope for the container, if one has been specified. 

# [Portal](#tab/portal)

To upload a blob with an encryption scope specified in the Azure portal, first create the encryption scope as described in [Create an encryption scope](#create-an-encryption-scope). Next, follow these steps to create the blob:

1. Navigate to the container to which you want to upload the blob.
1. Select the **Upload** button, and locate the blob to upload.
1. Expand the **Advanced** settings in the **Upload blob** pane.
1. Locate the **Encryption scope** drop-down section. By default, the blob is created with the default encryption scope for the container, if one has been specified. If the container requires that blobs use the default encryption scope, this section is disabled.
1. To specify a different scope for the blob that you are uploading, select **Choose an existing scope**, then select the desired scope from the drop-down.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Screenshot showing how to upload a blob with an encryption scope":::

# [PowerShell](#tab/powershell)

To upload a blob with an encryption scope specified by using PowerShell, call the [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) command and provide the encryption scope for the blob.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# [Azure CLI](#tab/cli)

To upload a blob with an encryption scope specified by using Azure CLI, call the [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) command and provide the encryption scope for the blob.

If you are using Azure Cloud Shell, follow the steps described in [Upload a blob](storage-quickstart-blobs-cli.md#upload-a-blob) to create a file in the root directory. You can then upload this file to a blob using the following sample.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## Change the encryption key for a scope

# [Portal](#tab/portal)

To change the key that protects a scope in the Azure portal, follow these steps:

1. Navigate to the **Encryption Scopes** tab to view the list of encryption scopes for the storage account.
1. Select the **More** button next to the scope you wish to modify.
1. In the **Edit encryption scope** pane, you can change the encryption type from Microsoft-managed key to customer-managed key or vice versa.
1. To select a new customer-managed key, select **Use a new key** and specify the key vault, key, and key version.

# [PowerShell](#tab/powershell)

To change the key that protects an encryption scope from a customer-managed key to a Microsoft-managed key with PowerShell, call the **Update-AzStorageEncryptionScope** command and pass in the `-StorageEncryption` parameter:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

To change the key that protects an encryption scope from a Microsoft-managed key to a customer-managed key, first make sure that you have enabled customer-managed keys with Azure Key Vault for the storage account. For more information, see [Configure customer-managed keys with Azure Key Vault by using PowerShell](../common/storage-encryption-keys-powershell.md). Next, call the **Update-AzStorageEncryptionScope** command and pass in the `-KeyUri` and `-KeyvaultEncryption` parameters:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# [Azure CLI](#tab/cli)

To change the key that protects an encryption scope from a customer-managed key to a Microsoft-managed key with Azure CLI, call the [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) command and pass in the `--key-source` parameter with the value `Microsoft.Storage`:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

To change the key that protects an encryption scope from a Microsoft-managed key to a customer-managed key, first make sure that you have enabled customer-managed keys with Azure Key Vault for the storage account. For more information, see [Configure customer-managed keys with Azure Key Vault by using Azure CLI](../common/storage-encryption-keys-cli.md). Next, call the **az storage account encryption-scope update** command, pass in the `--key-uri` parameter, and pass in the `--key-source` parameter with the value `Microsoft.KeyVault`:

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## Disable an encryption scope

# [Portal](#tab/portal)

To disable an encryption scope in the Azure portal, navigate to the **Encryption Scopes** setting for the storage account, select the desired encryption scope, and select **Disable**.

# [PowerShell](#tab/powershell)

To disable an encryption scope with PowerShell, call the Update-AzStorageEncryptionScope command and include the `-State` parameter with a value of `disabled`, as shown in the following example. To re-enable an encryption scope, call the same command with the `-State` parameter set to `enabled`. Remember to replace the placeholder values in the example with your own values:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# [Azure CLI](#tab/cli)

To disable an encryption scope with Azure CLI, call the [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) command and include the `--state` parameter with a value of `Disabled`, as shown in the following example. To re-enable an encryption scope, call the same command with the `--state` parameter set to `Enabled`. Remember to replace the placeholder values in the example with your own values:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## Next steps

- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md)
- [Use customer-managed keys with Azure Key Vault to manage Azure Storage encryption](../common/encryption-customer-managed-keys.md)
