---
title: FAQ - Backing up Azure VMs
description: In this article, discover answers to common questions about backing up Azure VMs with the Azure Backup service.
ms.topic: conceptual
ms.date: 09/17/2019
---
# Frequently asked questions-Back up Azure VMs

This article answers common questions about backing up Azure VMs with the [Azure Backup](./backup-overview.md) service.

## Backup

### Which VM images can be enabled for backup when I create them?

When you create a VM, you can enable backup for VMs running [supported operating systems](backup-support-matrix-iaas.md#supported-backup-actions).

### Why Initial backup is taking lot of time to complete?

Initial backup is always a full backup and it will depend on the size of the data and when the backup is processed. <br>
To improve backup performance see, [backup best practices](./backup-azure-vms-introduction.md#best-practices); [Backup considerations](./backup-azure-vms-introduction.md#backup-and-restore-considerations) and [Backup Performance](./backup-azure-vms-introduction.md#backup-performance)<br>
Although the total backup time for incremental backups is less than 24 hours, that might not be the case for the first backup.

### Is the backup cost included in the VM cost?

No. Backup costs are separate from a VM's costs. Learn more about [Azure Backup pricing](https://azure.microsoft.com/pricing/details/backup/).

### Which permissions are required to enable backup for a VM?

If you're a VM contributor, you can enable backup on the VM. If you're using a custom role, you need the following permissions to enable backup on the VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

If your Recovery Services vault and VM have different resource groups, make sure you have write permissions in the resource group for the Recovery Services vault.  

### Does an on-demand backup job use the same retention schedule as scheduled backups?

No. Specify the retention range for an on-demand backup job. By default, it's retained for 30 days when triggered from the portal.

### I recently enabled Azure Disk Encryption on some VMs. Will my backups continue to work?

Provide permissions for Azure Backup to access the Key Vault. Specify the permissions in PowerShell as described in the **Enable backup** section in the [Azure Backup PowerShell](backup-azure-vms-automation.md) documentation.

### I migrated VM disks to managed disks. Will my backups continue to work?

Yes, backups work seamlessly. There's no need to reconfigure anything.

### Why can't I see my VM in the Configure Backup wizard?

The wizard only lists VMs in the same region as the vault, and that aren't already being backed up.

### My VM is shut down. Will an on-demand or a scheduled backup work?

Yes. Backups run when a machine is shut down. The recovery point is marked as crash consistent.

### Can I cancel an in-progress backup job?

Yes. You can cancel the backup job in a **Taking snapshot** state. You can't cancel a job if data transfer from the snapshot is in progress.

### I enabled a lock on the resource group created by Azure Backup Service (for example, `AzureBackupRG_<geo>_<number>`). Will my backups continue to work?

If you lock the resource group created by the Azure Backup Service, backups will start to fail as there's a maximum limit of 18 restore points.

Remove the lock, and clear the restore point collection from that resource group to make the future backups successful. [Follow these steps](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) to remove the restore point collection.

### I have a lock at the resource group level that contains all the resources related to my virtual machine. Will my backup work?

Azure Backup creates a separate resource group in the format `AzureBackupRG_<geo>_<number>` to store ResourcePointCollections objects. Since this resource group is service owned, locking it will cause backups to fail. Locks can be only applied to customer-created resource groups.

### Does Azure Backup support standard SSD-managed disks?

Yes, Azure Backup supports [standard SSD managed disks](../virtual-machines/disks-types.md#standard-ssd).

### Can we back up a VM with a Write Accelerator (WA)-enabled disk?

Snapshots can be taken on only data disks which are WA enabled and not OS disks. So only data disks which are WA enabled can be protected.

### I have a VM with Write Accelerator (WA) disks and SAP HANA installed. How do I back up?

Azure Backup can back up the WA-enabled data disk. However, the backup won't provide database consistency.

Azure Backup provides a streaming backup solution for SAP HANA databases with an RPO of 15 minutes. It's Backint certified by SAP to provide a native backup support leveraging SAP HANA’s native APIs. Learn more [about backing up SAP HANA databases in Azure VMs](./sap-hana-db-about.md).

### What is the maximum delay I can expect in backup start time from the scheduled backup time I have set in my VM backup policy?

The scheduled backup will be triggered within 2 hours of the scheduled backup time. For example, If 100 VMs have their backup start time scheduled at 2:00 AM, then by 4:00 AM at the latest all the 100 VMs will have their backup job in progress. If scheduled backups have been paused because of an outage and resumed or retried, then the backup can start outside of this scheduled two-hour window.

### What is the minimum allowed retention range for a daily backup point?

Azure Virtual Machine backup policy supports a minimum retention range from seven days up to 9999 days. Any modification to an existing VM backup policy with less than seven days will require an update to meet the minimum retention range of seven days.

### What happens if I change the case of the name of my VM or my VM resource group?

If you change the case (to upper or lower) of your VM or VM resource group, the case of the backup item name won't change. However, this is expected Azure Backup behavior. The case change won't appear in the backup item, but is updated at the backend.

### Can I back up or restore selective disks attached to a VM?

Azure Backup now supports selective disk backup and restore using the Azure Virtual Machine backup solution. For more information, see [Selective disk backup and restore for Azure VMs](selective-disk-backup-restore.md).

### Are managed identities preserved if a tenant change occurs during backup?

If [tenant changes](/azure/devops/organizations/accounts/change-azure-ad-connection) occur, you're required to disable and re-enable [managed identities](../active-directory/managed-identities-azure-resources/overview.md) to make backups work again.

## Restore

### How do I decide whether to restore disks only or a full VM?

Think of a VM restore as a quick create option for an Azure VM. This option changes disk names, containers used by the disks, public IP addresses, and network interface names. The change maintains unique resources when a VM is created. The VM isn't added to an availability set.

You can use the restore disk option if you want to:

- Customize the VM that gets created. For example, change the size.
- Add configuration settings that weren't there at the time of backup.
- Control the naming convention for resources that are created.
- Add the VM to an availability set.
- Add any other setting that must be configured using PowerShell or a template.

### Can I restore backups of unmanaged VM disks after I upgrade to managed disks?

Yes, you can use backups taken before disks were migrated from unmanaged to managed.

### How do I restore a VM to a restore point before the VM was migrated to managed disks?

The restore process remains the same. If the recovery point is of a point-in-time when VM had unmanaged disks, you can [restore disks as unmanaged](tutorial-restore-disk.md#unmanaged-disks-restore). If the VM had managed disks, then you can [restore disks as managed disks](tutorial-restore-disk.md#managed-disk-restore). Then you can [create a VM from those disks](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Learn more](backup-azure-vms-automation.md#restore-an-azure-vm) about doing this in PowerShell.

### If the restore fails to create the VM, what happens to the disks included in the restore?

In the event of a managed VM restore, even if the VM creation fails, the disks will still be restored.

### Can I restore a VM that's been deleted?

Yes. Even if you delete the VM, you can go to the corresponding backup item in the vault and restore from a recovery point.

### How do I restore a VM to the same availability sets?

For Managed Disk Azure VMs, restoring to the availability sets is enabled by providing an option in the template while restoring as managed disks. This template has the input parameter called **Availability sets**.

### How do we get faster restore performances?

[Instant Restore](backup-instant-restore-capability.md) capability helps with faster backups and instant restores from the snapshots.

### What happens when we change the key vault settings for the encrypted VM?

After you change the key vault settings for the encrypted VM, backups will continue to work with the new set of details. However, after the restore from a recovery point before the change, you'll have to restore the secrets in a key vault before you can create the VM from it. For more information, see this [article](./backup-azure-restore-key-secret.md).

Operations like secret/key roll-over don't require this step and the same key vault can be used after restore.

### Can I access the VM once restored due to a VM having broken relationship with domain controller?

Yes, you access the VM once restored due to a VM having broken relationship with domain controller. For more information, see this [article](./backup-azure-arm-restore-vms.md#post-restore-steps).

### Can I cancel an in-progress restore job?
No, you cannot cancel the restore job that is in-progress.

### Why restore operation is taking long time to complete?

The total restore time depends on the Input/output operations per second (IOPS) and the throughput of the storage account. The total restore time can be affected if the target storage account is loaded with other application read and write operations. To improve restore operation, select a storage account that isn't loaded with other application data.

### How do we handle "Create New Virtual Machine"-restore type conflicts with governance policies?

Azure Backup uses "attach" disks from recovery points and doesn't look at your image references or galleries. So in the policy you can check "storageProfile.osDisk.createOption as Attach", and the script condition will be:

`if (storageProfile.osDisk.createOption == "Attach") then { exclude <Policy> }`

## Manage VM backups

### What happens if I modify a backup policy?

The VM is backed up using the schedule and retention settings in the modified or new policy.

- If retention is extended, existing recovery points are marked and kept in accordance with the new policy.
- If retention is reduced, recovery points are marked for pruning in the next cleanup job, and subsequently deleted.

### How do I move a VM backed up by Azure Backup to a different resource group?

1. Temporarily stop the backup and retain backup data.
2. To move virtual machines configured with Azure Backup, do the following steps:

   1. Find the location of your virtual machine.
   2. Find a resource group with the following naming pattern: `AzureBackupRG_<location of your VM>_1`. For example, *AzureBackupRG_westus2_1*
   3. In the Azure portal, check **Show hidden types**.
   4. Find the resource with type **Microsoft.Compute/restorePointCollections** that has the naming pattern `AzureBackup_<name of your VM that you're trying to move>_###########`.
   5. Delete this resource. This operation deletes only the instant recovery points, not the backed-up data in the vault.
   6. After the delete operation is complete, you can move your virtual machine.

3. Move the VM to the target resource group.
4. Resume the backup.

You can restore the VM from available restore points that were created before the move operation.

### What happens after I move a VM to a different resource group?

Once a VM is moved to a different resource group, it's a new VM as far as Azure Backup is concerned.

After moving the VM to a new resource group, you can reprotect the VM either in the same vault or a different vault. Since this is a new VM for Azure Backup, you'll be billed for it separately.

The old VM's restore points will be available for restore if needed. If you don't need this backup data, you can stop protecting your old VM with delete data.

### Is there a limit on number of VMs that can be associated with the same backup policy?

Yes, there's a limit of 100 VMs that can be associated to the same backup policy from the portal. We recommend that for more than 100 VMs, create multiple backup policies with same schedule or different schedule.

### How can I view the retention settings for my backups?

Currently, you can view retention settings at a backup item (VM) level based on the backup policy that's assigned to the VM.

One way to view the retention settings for your backups, is to navigate to the backup item [dashboard](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) for your VM, in the Azure portal. Selecting the link to its backup policy helps you view the retention duration of all the daily, weekly, monthly and yearly retention points associated with the VM.

You can also use [Backup Explorer](./monitor-azure-backup-with-backup-explorer.md) to view the retention settings for all your VMs within a single pane of glass. Navigate to Backup Explorer from any Recovery Services vault, go to the **Backup Items** tab and select the Advanced View to see detailed retention information for each VM.
