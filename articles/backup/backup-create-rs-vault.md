---
title: Create and configure Recovery Services vaults
description: In this article, learn how to create and configure Recovery Services vaults that store the backups and recovery points.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions 
---

# Create and configure a Recovery Services vault

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## Set storage redundancy

Azure Backup automatically handles storage for the vault. You need to specify how that storage is replicated.

> [!NOTE]
> Changing **Storage Replication type** (Locally redundant/ Geo-redundant) for a Recovery services vault has to be done before configuring backups in the vault. Once you configure backup, the option to modify is disabled.
>
>- If you haven't yet configured the backup, then [follow these steps](#set-storage-redundancy) to review and modify the settings.
>- If you've already configured the backup and must move from GRS to LRS, then [review these workarounds](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. From the **Recovery Services vaults** blade, click the new vault. Under the **Settings** section, click  **Properties**.
1. In **Properties**, under **Backup Configuration**, click **Update**.

1. Select the storage replication type, and click **Save**.

     ![Set the storage configuration for new vault](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - We recommend that if you're using Azure as a primary backup storage endpoint, continue to use the default **Geo-redundant** setting.
   - If you don't use Azure as a primary backup storage endpoint, then choose **Locally redundant**, which reduces the Azure storage costs.
   - Learn more about [geo](../storage/common/storage-redundancy.md) and [local](../storage/common/storage-redundancy.md) redundancy.

>[!NOTE]
>The Storage Replication settings for the vault are not relevant for Azure file share backup as the current solution is snapshot based and there is no data transferred to the vault. Snapshots are stored in the same storage account as the backed up file share.

## Set Cross Region Restore

As one of the restore options, Cross Region Restore (CRR) allows you to restore Azure VMs in a secondary region, which is an [Azure paired region](../best-practices-availability-paired-regions.md). This option allows you to:

- conduct drills when there's an audit or compliance requirement
- restore the VM or its disk if there's a disaster in the primary region.

To choose this feature, select **Enable Cross Region Restore** from the **Backup Configuration** blade.

For this process, there are pricing implications as it is at the storage level.

>[!NOTE]
>Before you begin:
>
>- Review the [support matrix](backup-support-matrix.md#cross-region-restore) for a list of supported managed types and regions.
>- The Cross Region Restore (CRR) feature is now previewed in all Azure public regions.
>- CRR is a vault level opt-in feature for any GRS vault (turned off by default).
>- After opting-in, it might take up to 48 hours for the backup items to be available in secondary regions.
>- Currently CRR is supported only for Backup Management Type - ARM Azure VM (classic Azure VM will not be supported).  When additional management types support CRR, then they will be **automatically** enrolled.
>- Cross Region Restore currently cannot be reverted back to GRS or LRS once the protection is initiated for the first time.

### Configure Cross Region Restore

A vault created with GRS redundancy includes the option to configure the Cross Region Restore feature. Every GRS vault will have a banner, which will link to the documentation. To configure CRR for the vault, go to the Backup Configuration blade, which contains the option to enable this feature.

 ![Backup Configuration banner](./media/backup-azure-arm-restore-vms/banner.png)

1. From the portal, go to Recovery Services vault > Settings > Properties.
2. Click **Enable Cross Region Restore in this vault** to enable the functionality.

   ![Before you click Enable Cross Region restore in this vault](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![After you click Enable Cross Region restore in this vault](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

Learn how to [view backup items in the secondary region](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region).

Learn how to [restore in the secondary region](backup-azure-arm-restore-vms.md#restore-in-secondary-region).

Learn how to [monitor secondary region restore jobs](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs).

## Modifying default settings

We highly recommend you review the default settings for **Storage Replication type** and **Security settings** before configuring backups in the vault.

- **Storage Replication type** by default is set to **Geo-redundant** (GRS). Once you configure the backup, the option to modify is disabled.
  - If you haven't yet configured the backup, then [follow these steps](#set-storage-redundancy) to review and modify the settings.
  - If you've already configured the backup and must move from GRS to LRS, then [review these workarounds](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- **Soft delete** by default is **Enabled** on newly created vaults to protect backup data from accidental or malicious deletes. [Follow these steps](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) to review and modify the settings.

### How to change from GRS to LRS after configuring backup

Before deciding to move from GRS to locally redundant storage (LRS), review the trade-offs between lower cost and higher data durability that fit your scenario. If you must move from GRS to LRS, then you have two choices. They depend on your business requirements to retain the backup data:

- [Don’t need to preserve previous backed-up data](#dont-need-to-preserve-previous-backed-up-data)
- [Must preserve previous backed-up data](#must-preserve-previous-backed-up-data)

#### Don’t need to preserve previous backed-up data

To protect workloads in a new LRS vault, the current protection and data will need to be deleted in the GRS vault and backups configured again.

>[!WARNING]
>The following operation is destructive and can't be undone. All backup data and backup items associated with the protected server will be permanently deleted. Proceed with caution.

Stop and delete current protection on the GRS vault:

1. Disable soft delete in the GRS vault properties. Follow [these steps](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) to disable soft delete.

1. Stop protection and delete backups from the existing GRS vault. In the Vault dashboard menu, select **Backup Items**. Items listed here that need to be moved to the LRS vault must be removed along with their backup data. See how to [delete protected items in the cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) and [delete protected items on premises](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. If you're planning to move AFS (Azure file shares), SQL servers or SAP HANA servers, then you'll need also to unregister them. In the vault dashboard menu, select **Backup Infrastructure**. See how to [unregister the SQL server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [unregister a storage account associated with Azure file shares](manage-afs-backup.md#unregister-a-storage-account), and [unregister an SAP HANA instance](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Once they're removed from the GRS vault, continue to configure the backups for your workload in the new LRS vault.

#### Must preserve previous backed-up data

If you need to keep the current protected data in the GRS vault and continue the protection in a new LRS vault, there are limited options for some of the workloads:

- For MARS, you can [stop protection with retain data](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) and register the agent in the new LRS vault.

  - Azure Backup service will continue to retain all the existing recovery points of the GRS vault.
  - You'll need to pay to keep the recovery points in the GRS vault.
  - You'll be able to restore the backed-up data only for unexpired recovery points in the GRS vault.
  - A new initial replica of the data will need to be created on the LRS vault.

- For an Azure VM, you can [stop protection with retain data](backup-azure-manage-vms.md#stop-protecting-a-vm) for the VM in the GRS vault, move the VM to another resource group, and then protect the VM in the LRS vault. See [guidance and limitations](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) for moving a VM to another resource group.

  A VM can be protected in only one vault at a time. However, the VM in the new resource group can be protected on the LRS vault as it is considered a different VM.

  - Azure Backup service will retain the recovery points that have been backed up on the GRS vault.
  - You'll need to pay to keep the recovery points in the GRS vault (see [Azure Backup pricing](azure-backup-pricing.md) for details).
  - You'll be able to restore the VM, if needed, from the GRS vault.
  - The first backup on the LRS vault of the VM in the new resource will be an initial replica.


## Next steps

[Learn about](backup-azure-recovery-services-vault-overview.md) Recovery Services vaults.
[Learn about](backup-azure-delete-vault.md) Delete Recovery Services vaults.
