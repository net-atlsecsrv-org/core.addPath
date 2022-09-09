---
title: Frequently asked questions about Azure Disk Backup
description: Get answers to frequently asked questions about Azure Disk Backup
ms.topic: conceptual
ms.date: 01/07/2021
---

# Frequently asked questions about Azure Disk Backup

This article answers frequently asked questions about Azure Disk Backup. For more information on the [Azure Disk backup](disk-backup-overview.md) region availability, supported scenarios and limitations, see the [support matrix](disk-backup-support-matrix.md).

## Frequently asked questions

### Can I back up the disk using the Azure Disk Backup solution if the same disk is backed up using Azure virtual machine backup?

Azure Backup offers side-by-side support for backup of managed disk using Disk backup and the [Azure VM backup](backup-azure-vms-introduction.md) solutions. This is useful when you need once-a-day application consistent backup of virtual machines and also more frequent backups of the OS disk or a specific data disk, which are crash consistent without impacting the production application performance.

### How do I find the snapshot resource group that I used to configure backup for a disk?

In the **Backup Instance** screen, you can find the snapshot resource group field in the **Essentials** section. You can search and select your backup instance of  the corresponding disk from Backup center or the Backup vault.

![Snapshot resource group field](./media/disk-backup-faq/snapshot-resource-group.png)

### What is a snapshot resource group?

Azure Disk Backup offers operational tier backup for managed disk. That is, the snapshots that are created during the scheduled and on-demand backup operations are stored in a resource group within your subscription. Azure Backup offers instant restore because the incremental snapshots are stored within your subscription. This resource group is known as the snapshot resource group. For more information, see [Configure backup](backup-managed-disks.md#configure-backup).

### Why must the snapshot resource group be in same subscription as that of the disk being backed up?

You can't create an incremental snapshot for a particular disk outside of that disk's subscription. So choose the resource group within the same subscription as that of the disk to be backed up. Learn more about [incremental snapshot](../virtual-machines/disks-incremental-snapshots.md#restrictions) for managed disks.

### Why do I need to provide role assignments to be able to configure backups, perform scheduled and on-demand backups, and restore operations?

Azure Disk Backup uses the least privilege approach to discover, protect, and restore the managed disks in your subscriptions. To achieve this, Azure Backup uses the managed identity of the [Backup vault](backup-vault-overview.md) to access other Azure resources. A system assigned managed identity is restricted to one per resource and is tied to the lifecycle of this resource. You can grant permissions to the managed identity by using Azure role-based access control (Azure RBAC). Managed identity is a service principal of a special type that may only be used with Azure resources. Learn more about [managed identities](../active-directory/managed-identities-azure-resources/overview.md). By default, the Backup vault won't have permission to access the disk to be backed up, create periodic snapshots, delete snapshots after retention period, and to restore a disk from backup. By explicitly granting role assignments to the Backup vault's managed identity, you're in control of managing permissions to the resources on the subscriptions.

### Why does backup policy limit the retention duration?

Azure Disk Backup uses incremental snapshots, which are limited to 200 snapshots per disk. To allow you to take on-demand backups aside from scheduled backups, backup policy limits the total backups to 180. Learn more about [incremental snapshots](../virtual-machines/disks-incremental-snapshots.md#restrictions) for managed disks.

### How does the hourly and daily backup frequency work in the backup policy?

Azure Disk Backup offers multiple backups per day. If you require more frequent backups, choose the **Hourly** backup frequency. The backups are scheduled based on the **Time** interval selected. For example, if you select **Every 4 hours**, then the backups are taken at approximately every 4 hours so that the backups are distributed equally across the day. If once a day backup is sufficient enough, then choose the **Daily** backup frequency. In the daily backup frequency, you can specify the time of the day when your backups will be taken. It's important to note that the time of the day indicates the backup start time and not the time when the backup completes. The time required to complete the backup operation is dependent on various factors including the churn rate between consecutive backups. However, Azure Disk backup is an agentless backup that uses [incremental snapshots](../virtual-machines/disks-incremental-snapshots.md) that don't impact the production application performance.

### Why does the Backup vault’s redundancy setting not apply to the backups stored in operational tier (the snapshot resource group)?

Azure Backup uses [incremental snapshots](../virtual-machines/disks-incremental-snapshots.md#restrictions) of managed disks that store only the delta changes to disks since the last snapshot on Standard HDD storage, regardless of the storage type of the parent disk. For more reliability, incremental snapshots are stored on Zone Redundant Storage (ZRS) by default in regions that support ZRS. Currently, Azure Disk Backup supports operational backups of managed disks that don't copy the backups to Backup vault storage. So the backup storage redundancy setting of the Backup vault doesn't apply to the recovery points.

### Can I use Backup Center to configure backups and manage backup instances for Azure Disks?

Yes, Azure Disk Backup is integrated into [Backup Center](backup-center-overview.md), which provides a **single unified management experience** in Azure for enterprises to govern, monitor, operate, and analyze backups at scale. You can also use Backup vault to back up, restore, and manage the backup instances that are protected within the vault.

### Why do I need to create a Backup vault and not use a Recovery Services vault?

A Backup vault is a storage entity in Azure that houses backup data for certain newer workloads that Azure Backup supports. You can use Backup vaults to hold backup data for various Azure services, such Azure Database for PostgreSQL servers, Azure Disks, and newer workloads that Azure Backup will support. Backup vaults make it easy to organize your backup data, while minimizing management overhead. Refer to [Backup vaults](./backup-vault-overview.md) to learn more.

### Can the disk to be backed up and the Backup vault be in different subscriptions?

Yes, the source-managed disk to be backed up and the Backup vault can be in different subscriptions.

### Can the disk to be backed up and the Backup vault be in different regions?

No, currently the source-managed disk to be backed up and the Backup vault must be in the same region.

### Can I restore a disk into a different subscription?

Yes, you can restore the disk onto a different subscription than that of the source-managed disk from which the backup is taken.

### Can I back up multiple disks together?

No, point-in-time snapshots of multiple disks attached to a virtual machine isn't supported. For more information, see [Configure backup](backup-managed-disks.md#configure-backup) and to learn more about limitations, refer to the [support matrix](disk-backup-support-matrix.md).

### What are my options to back up disks across multiple subscriptions?

Currently, using the Azure portal to configure backup of disks is limited to a maximum of 20 disks from the same subscription.

### What is a target resource group?

During a restore operation, you can choose the subscription and a resource group where you want to restore the disk to. Azure Backup will create new disks from the recovery point in the selected resource group. This is referred to as a target resource group. Note that the Backup vault's managed identity requires the role assignment on the target resource group to be able to perform restore operation successfully. For more information, see the [restore documentation](restore-managed-disks.md).

### What are the permissions used by Azure Backup during backup and restore operation?

Following are the actions used in the **Disk Backup Reader** role assigned on the **disk** to be backed up:

"Microsoft.Compute/disks/read"

"Microsoft.Compute/disks/beginGetAccess/action"

Following are the actions used in the **Disk Snapshot Contributor** role assigned on the **Snapshot resource group**:

"Microsoft.Compute/snapshots/delete"

"Microsoft.Compute/snapshots/write"

“Microsoft.Compute/snapshots/read"

"Microsoft.Storage/storageAccounts/write"

"Microsoft.Storage/storageAccounts/read"

"Microsoft.Storage/storageAccounts/delete"

"Microsoft.Resources/subscriptions/resourceGroups/read"

"Microsoft.Storage/storageAccounts/listkeys/action"

"Microsoft.Compute/snapshots/beginGetAccess/action"

"Microsoft.Compute/snapshots/endGetAccess/action"

"Microsoft.Compute/disks/beginGetAccess/action"

Following are the actions used in the **Disk Restore Operator** role assigned on **Target Resource Group**:

"Microsoft.Compute/disks/write"

"Microsoft.Compute/disks/read"

"Microsoft.Resources/subscriptions/resourceGroups/read"

>[!NOTE]
>The permissions on these roles may change in the future, based on the features being added by the Azure Backup service.

## Next steps

- [Azure Disk Backup support matrix](disk-backup-support-matrix.md)