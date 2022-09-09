---
title: "TDE - Azure Key Vault Integration or Bring Your Own Key (BYOK) - Azure SQL Database| Microsoft Docs"
description: "Bring Your Own Key (BYOK) support for Transparent Data Encryption (TDE) with Azure Key Vault for SQL Database and Data Warehouse. TDE with BYOK overview, benefits, how it works, considerations, and recommendations."
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: 
ms.devlang: 
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 07/18/2019
---
# Azure SQL Transparent Data Encryption with customer-managed keys in Azure Key Vault: Bring Your Own Key support

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) with Azure Key Vault integration allows to encrypt the Database Encryption Key (DEK) with a customer-managed asymmetric key called TDE Protector. This is also generally referred to as Bring Your Own Key (BYOK) support for Transparent Data Encryption.  In the BYOK scenario, the TDE Protector is stored in a customer-owned and managed [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Azure’s cloud-based external key management system. The TDE Protector can be [generated](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) by the key vault or [transferred](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) to the key vault from an on-prem HSM device. The TDE DEK, which is stored on the boot page of a database, is encrypted and decrypted by the TDE Protector stored in Azure Key Vault, which it never leaves.  SQL Database needs to be granted permissions to the customer-owned key vault to decrypt and encrypt the DEK. If permissions of the logical SQL server to the key vault are revoked, a database will be inaccessible, connections will be denied and all data is encrypted. For Azure SQL Database, the TDE protector is set at the logical SQL server level and is inherited by all databases associated with that server. For [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), the TDE protector is set at the instance level and it is inherited by all *encrypted* databases on that instance. The term *server* refers both to server and instance throughout this document, unless stated differently.

> [!NOTE]
> Transparent Data Encryption with Azure Key Vault integration (Bring Your Own Key) for Azure SQL Database Managed Instance is in preview.


With TDE with Azure Key Vault integration, users can control key management tasks including key rotations, key vault permissions, key backups, and enable auditing/reporting on all TDE protectors using Azure Key Vault functionality. Key Vault provides central key management, leverages tightly monitored hardware security modules (HSMs), and enables separation of duties between management of keys and data to help meet compliance with security policies.  

TDE with Azure Key Vault integration provides the following benefits:

- Increased transparency and granular control with the ability to self-manage the TDE protector
- Ability to revoke permissions at any time to render database inaccessible
- Central management of TDE protectors (along with other keys and secrets used in other Azure services) by hosting them in Key Vault
- Separation of key and data management responsibilities within the organization, to support separation of duties
- Greater trust from your own clients, since Key Vault is designed so that Microsoft does not see or extract any encryption keys.
- Support for key rotation

> [!IMPORTANT]
> For those using service-managed TDE who would like to start using Key Vault, TDE remains enabled during the process of switching over to a TDE protector in Key Vault. There is no downtime nor re-encryption of the database files. Switching from a service-managed key to a Key Vault key only requires re-encryption of the database encryption key (DEK), which is a fast and online operation.

## How does TDE with Azure Key Vault integration support work

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

![Authentication of the Server to the Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

When TDE is first configured to use a TDE protector from Key Vault, the server sends the DEK of each TDE-enabled database to Key Vault for a wrap key request. Key Vault returns the encrypted database encryption key, which is stored in the user database.  

> [!IMPORTANT]
> It is important to note that **once a TDE Protector is stored in Azure Key Vault, it never leaves the Azure Key Vault**. The server can only send key operation requests to the TDE protector key material within Key Vault, and **never accesses or caches the TDE protector**. The Key Vault administrator has the right to revoke Key Vault permissions of the server at any point, in which case all connections to the database are denied.

## Guidelines for configuring TDE with Azure Key Vault

### General Guidelines

- Ensure Azure Key Vault and Azure SQL Database/Managed Instance are going to be in the same tenant.  Cross-tenant key vault and server interactions **are not supported**.
- If you are planning a tenant move, TDE with AKV will have to be reconfigured, learn more about [moving resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- When configuring TDE with Azure Key Vault, it is important to consider the load placed on the key vault by repeated wrap/unwrap operations. For example, since all databases associated with a SQL Database server use the same TDE protector, a failover of that server will trigger as many key operations against the vault as there are databases in the server. Based on our experience and documented [key vault service limits](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), we recommend associating at most 500 Standard / General Purpose or 200 Premium / Business Critical databases with one Azure Key Vault in a single subscription to ensure consistently high availability when accessing the TDE protector in the vault.
- Recommended: Keep a copy of the TDE protector on premises.  This requires an HSM device to create a TDE Protector locally and a key escrow system to store a local copy of the TDE Protector.  Learn [how to transfer a key from a local HSM to Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### Guidelines for configuring Azure Key Vault

- Create a key vault with [soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) and purge protection enabled to protect from data loss in case of accidental key – or key vault – deletion. You must enable “soft-delete” property on the key vault via [CLI](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete) or [Powershell](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) (this option is not available from the AKV Portal yet – but required by Azure SQL):  
  - Soft deleted resources are retained for a set period of time, 90 days unless they are recovered or purged.
  - The **recover** and **purge** actions have their own permissions associated in a key vault access policy.
- Set a resource lock on the key vault to control who can delete this critical resource and help to prevent accidental or unauthorized deletion.  [Learn more about resource locks](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Grant the SQL Database server access to the key vault using its Azure Active Directory (Azure AD) Identity.  When using the Portal UI, the Azure AD identity gets automatically created and the key vault access permissions are granted to the server.  Using PowerShell to configure TDE with BYOK, the Azure AD identity must be created and completion should be verified. See [Configure TDE with BYOK](transparent-data-encryption-byok-azure-sql-configure.md) and [Configure TDE with BYOK for Managed Instance](https://aka.ms/sqlmibyoktdepowershell) for detailed step-by-step instructions when using PowerShell.

   > [!NOTE]
   > If the Azure AD Identity **is accidentally deleted or the server’s permissions are revoked** using the key vault’s access policy or inadvertently by moving the server to a different tenant, the  server loses access to the key vault, and TDE encrypted databases will be inaccessible and logons are denied until the logical server’s Azure AD Identity and permissions have been restored.  

- When using firewalls and virtual networks with Azure Key Vault, you must allow trusted Microsoft services to bypass this firewall. Choose YES.

   > [!NOTE]
   > If TDE encrypted SQL databases lose access to the key vault because they cannot bypass the firewall, the databases will be inaccessible, and logons are denied until firewall bypass permissions have been restored.

- Enable auditing and reporting on all encryption keys: Key Vault provides logs that are easy to inject into other security information and event management (SIEM) tools. Operations Management Suite (OMS) [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) is one example of a service that is already integrated.
- To ensure high availability of encrypted databases, configure each SQL Database server with two Azure Key Vaults that reside in different regions.


### Guidelines for configuring the TDE Protector (asymmetric key)

- Create your encryption key locally on a local HSM device. Ensure this is an asymmetric, RSA 2048 or RSA HSM 2048 key so it is storable in Azure Key Vault.
- Escrow the key in a key escrow system.  
- Import the encryption key file (.pfx, .byok, or .backup) to Azure Key Vault.

   > [!NOTE]
   > For testing purposes, it is possible to create a key with Azure Key Vault, however this key cannot be escrowed, because  the private key can never leave the key vault.  Always back up and escrow keys used to encrypt production data, as the loss of the key (accidental deletion in key vault, expiration etc.) results in permanent data loss.

- If you use a key with an expiration date – implement an expiration warning system to rotate the key before it expires: **once the key expires, the encrypted databases lose access to their TDE Protector and will be inaccessible** and all logons will be denied until the key has been rotated to a new key and selected as the new key and default TDE Protector for the logical SQL server.
- Ensure the key is enabled and has permissions to perform *get*, *wrap key*, and *unwrap key* operations.
- Create an Azure Key Vault key backup before using the key in Azure Key Vault for the first time. Learn more about the [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) command.
- Create a new backup whenever any changes are made to the key (for example, add ACLs, add tags, add key attributes).
- **Keep previous versions** of the key in the key vault when rotating keys, so older database backups can be restored. When the TDE Protector is changed for a database, old backups of the database **are not updated** to use the latest TDE Protector.  Each backup needs the TDE Protector it was created with at restore time. Key rotations can be performed following the instructions at [Rotate the Transparent Data Encryption Protector Using PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Keep all previously used keys in Azure Key Vault after changing back to service-managed keys.  This ensures database backups can be restored with the TDE protectors stored in Azure Key Vault.  TDE protectors created with Azure Key Vault have to be maintained until all stored backups have been created with service-managed keys.  
- Make recoverable backup copies of these keys using [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- To remove a potentially compromised key during a security incident without the risk of data loss, follow the steps at [Remove a potentially compromised key](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### Guidelines for monitoring the TDE with Azure Key Vault configuration

If the logical SQL server loses access to the customer-managed TDE protector in Azure Key Vault, the database will deny all connections and appear inaccessible in the Azure portal.  The most common causes for this are:
- Key vault accidentally deleted or behind a firewall
- Key vault key accidentally deleted, disabled or expired
- The logical SQL Server instance AppId accidentally deleted
- Key specific permissions for logical SQL Server instance AppId revoked

 > [!NOTE]
 > The database will self-heal and become online automatically if the access to the customer-managed TDE Protector is restored within 48 hours.  If the database is inaccessible due to an intermittent networking outage, there is no action required and the databases will come back online automatically.
  
- For more information about troubleshooting existing configurations see [Troubleshoot TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- To monitor database state and to enable alerting for loss of TDE Protector access, configure the following Azure features:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). An inaccessible database that has lost access to the TDE Protector will show as "Unavailable" after the first connection to the database has been denied.
    - [Activity Log](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) when access to the TDE protector in the customer-managed key vault fails, entries are added to the activity log.  Creating alerts for these events will enable you to reinstate access as soon as possible.
    - [Action Groups](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) can be defined to send you notifications and alerts based on your preferences, e.g. Email/SMS/Push/Voice, Logic App, Webhook, ITSM, or Automation Runbook.
    

## High Availability, Geo-Replication, and Backup / Restore

### High availability and disaster recovery

How to configure high availability with Azure Key Vault depends on the configuration of your database and SQL Database server, and here are the recommended configurations for two distinct cases.  The first case is a stand-alone database or SQL Database server with no configured geo redundancy.  The second case is a database or SQL Database server configured with failover groups or geo-redundancy, where it must be ensured that each geo-redundant copy has a local Azure Key Vault within the failover group to ensure geo-failovers work.

In the first case, if you require high availability of a database and SQL Database server with no configured geo-redundancy, it is highly recommended to configure the server to use two different key vaults in two different regions with the same key material. This can be accomplished by creating a TDE protector using the primary Key Vault co-located in the same region as the SQL Database server and cloning the key into a key vault in a different Azure region, so that the server has access to a second key vault should the primary key vault experience an outage while the database is up and running. Use the Backup-AzKeyVaultKey cmdlet to retrieve the key in encrypted format from the primary key vault and then use the Restore-AzKeyVaultKey cmdlet and specify a key vault in the second region.

![Single-Server HA and no geo-dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## How to configure Geo-DR with Azure Key Vault

To maintain high availability of TDE Protectors for encrypted databases, it is required to configure redundant Azure Key Vaults based on the existing or desired SQL Database failover groups or active geo-replication instances.  Each geo-replicated server requires a separate key vault, that must be co-located with the server in the same Azure region. Should a primary database become inaccessible due to an outage in one region and a failover is triggered, the secondary database is able to take over using the secondary key vault.

For Geo-Replicated Azure SQL databases, the following Azure Key Vault configuration is required:

- One primary database with a key vault in region and one secondary database with a key vault in region.
- At least one secondary is required, up to four secondaries are supported.
- Secondaries of secondaries (chaining) are not supported.

The following section will go over the setup and configuration steps in more detail.

### Azure Key Vault Configuration Steps

- Install [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Create two Azure Key Vaults in two different regions using [PowerShell to enable the “soft-delete” property](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) on the key vaults (this option is not available from the AKV Portal yet – but required by SQL).
- Both Azure Key Vaults must be located in the two regions available in the same Azure Geo in order for backup and restore of keys to work.  If you need the two key vaults to be located in different geos to meet SQL Geo-DR requirements, follow the [BYOK Process](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) that allows keys to be imported from an on-premises HSM.
- Create a new key in the first key vault:  
  - RSA/RSA-HSM 2048 key
  - No expiration dates
  - Key is enabled and has permissions to perform get, wrap key, and unwrap key operations
- Back up the primary key and restore the key to the second key vault.  See [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) and [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### Azure SQL Database Configuration Steps

The following configuration steps differ whether starting with a new SQL deployment or if working with an already existing SQL Geo-DR deployment.  We outline the configuration steps for a new deployment first, and then explain how to assign TDE Protectors stored in Azure Key Vault to an existing deployment that already has a Geo-DR link established.

**Steps for a new deployment**:

- Create the two SQL Database servers in the same two regions as the previously created key vaults.
- Select the SQL Database server TDE pane, and for each SQL Database server:  
  - Select the AKV in the same region
  - Select the key to use as TDE Protector – each server will use the local copy of the TDE Protector.
  - Doing this in the Portal will create an [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) for the SQL Database server, which is used to assign the SQL Database server permissions to access the key vault – do not delete this identity. Access can be revoked by removing the permissions in Azure Key Vault instead for the SQL Database server, which is used to assign the SQL Database server permissions to access the key vault.
- Create the primary database.
- Follow the [active geo-replication guidance](sql-database-geo-replication-overview.md) to complete the scenario, this step will create the secondary database.

![Failover groups and geo-dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> It is important to ensure that the same TDE Protectors are present in both key vaults, before proceeding to establish the geo-link between the databases.

**Steps for an existing SQL DB with Geo-DR deployment**:

Because the SQL Database servers already exist, and primary and secondary databases are already assigned, the steps to configure Azure Key Vault must be performed in the following order:

- Start with the SQL Database server that hosts the secondary database:
  - Assign the key vault located in the same region
  - Assign the TDE Protector
- Now go to the SQL Database server that hosts the primary database:
  - Select the same TDE Protector as used for the secondary DB

![Failover groups and geo-dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> When assigning the key vault to the server, it is important to start with the secondary server.  In the second step assign the key vault to the primary server and update the TDE Protector, the Geo-DR link will continue to work because at this point the TDE Protector used by the replicated database is available to both servers.

Before enabling TDE with customer managed keys in Azure Key Vault for a SQL Database Geo-DR scenario, it is important to create and maintain two Azure Key Vaults with identical contents in the same regions that will be used for SQL Database geo-replication.  “Identical contents” specifically means that both key vaults must contain copies of the same TDE Protector(s) so that both servers have access to the TDE Protectors use by all databases.  Going forward, it is required to keep both key vaults in sync, which means they must contain the same copies of TDE Protectors after key rotation, maintain old versions of keys used for log files or backups, TDE Protectors must maintain the same key properties and the key vaults must maintain the same access permissions for SQL.  

Follow the steps in [Active geo-replication overview](sql-database-geo-replication-overview.md) to test and trigger a failover, which should be done on a regular basis to confirm the access permissions for SQL to both key vaults have been maintained.

### Backup and Restore

Once a database is encrypted with TDE using a key from Key Vault, any generated backups are also encrypted with the same TDE Protector.

To restore a backup encrypted with a TDE Protector from Key Vault, make sure that the key material is still in the original vault under the original key name. When the TDE Protector is changed for a database, old backups of the database **are not** updated to use the latest TDE Protector. Therefore, we recommend that you keep all old versions of the TDE Protector in Key Vault, so database backups can be restored.

If a key that might be needed for restoring a backup is no longer in its original key vault, the following error message  is returned:
"Target server `<Servername>` does not have access to all AKV Uris created between \<Timestamp #1> and \<Timestamp #2>. Please retry operation after restoring all AKV Uris."

To mitigate this, run the [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet to return the list of keys from Key Vault that were added to the server (unless they were deleted by a user). To ensure all backups can be restored, make sure the target server for the backup has access to all of these keys.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

To learn more about backup recovery for SQL Database, see [Recover an Azure SQL database](sql-database-recovery-using-backups.md). To learn more about backup recovery for SQL Data Warehouse, see [Recover an Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Additional consideration for backed up log files: Backed up log files remain encrypted with the original TDE Encryptor, even if the TDE Protector was rotated and the database is now using a new TDE Protector.  At restore time, both keys will be needed to restore the database.  If the log file is using a TDE Protector stored in Azure Key Vault, this key will be needed at restore time, even if the database has been changed to use service-managed TDE in the meantime.
