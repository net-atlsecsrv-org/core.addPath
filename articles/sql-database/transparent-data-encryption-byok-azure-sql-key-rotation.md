---
title: Rotate TDE protector - PowerShell
description: Learn how to rotate the Transparent Data Encryption (TDE) protector for an Azure SQL server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: 
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
---
# Rotate the Transparent Data Encryption (TDE) protector using PowerShell

This article describes key rotation for an Azure SQL server using a TDE protector from Azure Key Vault. Rotating an Azure SQL server’s TDE protector means switching to a new asymmetric key that protects the databases on the server. Key rotation is an online operation and should only take a few seconds to complete, because this only decrypts and re-encrypts the database’s data encryption key, not the entire database.

This guide discusses two options to rotate the TDE protector on the server.

> [!NOTE]
> A paused SQL Data Warehouse must be resumed before key rotations.

> [!IMPORTANT]
> Do not delete previous versions of the key after a rollover. When keys are rolled over, some data is still encrypted with the previous keys, such as older database backups.

## Prerequisites

- This how-to guide assumes that you are already using a key from Azure Key Vault as the TDE protector for an Azure SQL Database or Data Warehouse. See [Transparent Data Encryption with BYOK Support](transparent-data-encryption-byok-azure-sql.md).
- You must have Azure PowerShell installed and running.
- [Recommended but optional] Create the key material for the TDE protector in a hardware security module (HSM) or local key store first, and import the key material to Azure Key Vault. Follow the [instructions for using a hardware security module (HSM) and Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) to learn more.

# [PowerShell](#tab/azure-powershell)

For Az module installation instructions, see [Install Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

# [Azure CLI](#tab/azure-cli)

For installation, see [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## Manual key rotation

Manual key rotation uses the following commands to add a completely new key, which could be under a new key name or even another key vault. Using this approach supports adding the same key to different key vaults to support high-availability and geo-dr scenarios.

> [!NOTE]
> The combined length for the key vault name and key name cannot exceed 94 characters.

# [PowerShell](#tab/azure-powershell)

Use the [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), and [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# [Azure CLI](#tab/azure-cli)

Use the [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create), and [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) commands.

```azure-cli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## Useful PowerShell cmdlets

# [PowerShell](#tab/azure-powershell)

- To switch the TDE protector from Microsoft-managed to BYOK mode, use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- To switch the TDE protector from BYOK mode to Microsoft-managed, use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# [Azure CLI](#tab/azure-cli)

The following examples use [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- To switch the TDE protector from Microsoft-managed to BYOK mode,

   ```azure-cli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- To switch the TDE protector from BYOK mode to Microsoft-managed,

   ```azure-cli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## Next steps

- In case of a security risk, learn how to remove a potentially compromised TDE protector: [Remove a potentially compromised key](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Get started with Azure Key Vault integration and Bring Your Own Key support for TDE: [Turn on TDE using your own key from Key Vault using PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
