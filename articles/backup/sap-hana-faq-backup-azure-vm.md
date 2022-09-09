---
title: FAQ - Back up SAP HANA databases on Azure VMs
description: In this article, discover answers to common questions about backing up SAP HANA databases using the Azure Backup service.
ms.topic: conceptual
ms.date: 11/7/2019
---

# Frequently asked questions – Back up SAP HANA databases on Azure VMs

This article answers common questions about backing up SAP HANA databases using the Azure Backup service.

## Backup

### How many full backups are supported per day?

We support only one full backup per day. You can't have differential backup and full backup triggered on the same day.

### Do successful backup jobs create alerts?

No. Successful backup jobs don't generate alerts. Alerts are sent only for backup jobs that fail. Detailed behavior for portal alerts is documented [here](./backup-azure-monitoring-built-in-monitor.md). However, if you're interested having alerts even for successful jobs, you can use [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### Can I see scheduled backup jobs in the Backup Jobs menu?

The Backup Job menu will only show on-demand backup jobs. For scheduled jobs, use [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### Are future databases automatically added for backup?

No, this isn't currently supported.

### If I delete a database from an instance, what will happen to the backups?

If a database is dropped from an SAP HANA instance, the database backups are still attempted. This implies that the deleted database begins to show up as unhealthy under **Backup Items** and is still protected.
The correct way to stop protecting this database is to perform **Stop Backup with delete data** on this database.

### If I change the name of the database after it has been protected, what will the behavior be?

A renamed database is treated as a new database. Therefore, the service will treat this situation as if the database weren't found and will fail the backups. The renamed database will appear as a new database and must be configured for protection.

### What are the prerequisites to back up SAP HANA databases on an Azure VM?

Refer to the [prerequisites](tutorial-backup-sap-hana-db.md#prerequisites) and [What the pre-registration script does](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) sections.

### What permissions should be set so Azure can back up SAP HANA databases?

Running the pre-registration script sets the required permissions to allow Azure to back up SAP HANA databases. You can find more about what the pre-registration script does [here](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### Will backups work after migrating SAP HANA from SDC to MDC?

Refer to [this section](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) of the troubleshooting guide.

### What should be done while upgrading within the same version?

Refer to [this section](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) in the troubleshooting guide.

### Can Azure HANA Backup be set up against a virtual IP (load balancer) and not a virtual machine?

Currently we don't have the capability to set up the solution against a virtual IP alone. We need a virtual machine to execute the solution.

### How can I move an on-demand backup to the local file system instead of the Azure vault?

1. Wait for the currently running backup to complete on the desired database (check from studio for completion).
1. Disable log backups and set the catalog backup to **Filesystem** for the desired DB using the following steps:
1. Double-click **SYSTEMDB** -> **configuration** -> **Select Database** -> **Filter (log)**
    1. Set enable_auto_log_backup to **no**.
    1. Set catalog_backup_using_backint to **false**.
1. Take an on-demand backup (full / differential/ incremental) on the desired database, and wait for the backup and catalog backup to complete.
1. If you want to also move the log backups to the Filesystem, set enable_auto_log_backup to **yes**.
1. Revert to the previous settings to allow backups to flow to the Azure vault:
    1. Set enable_auto_log_backup to **yes**.
    1. Set catalog_backup_using_backint to **true**.

>[!NOTE]
>Moving backups to the local Filesystem and switching back again to the Azure vault may cause a log chain break of the log backups in the vault. This will trigger a full backup, which once successfully completed will start backing up the logs.

### How can I use SAP HANA Backup with my HANA Replication set-up?

Currently, Azure Backup doesn't have the capability to understand an HSR set-up. This means that the primary and secondary nodes of the HSR will be treated as two individual, unrelated VMs. You'll first need to configure backup on the primary node. When a fail-over happens, backup must be configured on the secondary node (which now becomes the primary node). There's no automatic fail-over of backup to the other node.

To back up data from the active (primary) node at any given point in time, you can **switch protection** to the secondary node, which has now become the primary after fail-over.

To perform this **switch protection**, follow these steps:

- [Stop protection](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (with retain data) on primary
- Run the [pre-registration script](https://aka.ms/scriptforpermsonhana) on the secondary node
- [Discover the databases](tutorial-backup-sap-hana-db.md#discover-the-databases) on the secondary node and [configure backups](tutorial-backup-sap-hana-db.md#configure-backup) on them

These steps must be performed manually after every fail-over. You can perform these steps through command line / HTTP REST in addition to the Azure portal. To automate these steps, you can use an Azure runbook.

Here is a detailed example of how **switch protection** must be performed:

In this example, you have two nodes - Node 1 (primary) and Node 2 (secondary) in the HSR set-up.  Backups are configured on Node 1. As mentioned above, don't attempt yet to configure backups on Node 2.

When the first failover happens, Node 2 becomes the primary. Then,

1. Stop protection of Node 1 (previous primary) with the retain data option.
1. Run the pre-registration script on Node 2 (which is now the primary).
1. Discover databases on Node 2, assign backup policy, and configure backups.

Then a first full backup is triggered on Node 2 and after that completes, log backups start.

When the next fail-over happens, Node 1 becomes primary again and Node 2 becomes secondary. Now repeat the process:

1. Stop protection of Node 2 with retain data option.
1. Run the pre-registration script on Node 1 (which has become the primary again)
1. Then [Resume backup](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) on Node 1 with the required policy (as the backups were stopped earlier on Node 1).

Then full backup will again be triggered on Node 1 and after that completes, log backups start.

## Restore

### Why can't I see the HANA system I want my database to be restored to?

Check if all the prerequisites for the restore to target SAP HANA instance are met. For more information, see [Prerequisites - Restore SAP HANA databases in Azure VM](./sap-hana-db-restore.md#prerequisites).

### Why is the Overwrite DB restore failing for my database?

Ensure that the **Force Overwrite** option is selected while restoring.

### Why do I see the "Source and target systems for restore are incompatible" error?

Refer to the SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) to see what restore types are currently supported.

### Can I use a backup of a database running on SLES to restore to an RHEL HANA system or vice versa?

Yes, you can use streaming backups triggered on a HANA database running on SLES to restore it to an RHEL HANA system and vice versa. That is, cross OS restore is possible using streaming backups. However, you'll have to ensure that the HANA system you want to restore to, and the HANA system used for restore, are both compatible for restore according to SAP. Refer to SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) to see which restore types are compatible.

## Policy

### Different options available during creation of a new policy for SAP HANA backup

Before creating a policy, you should be clear on the requirements of RPO and RTO and its relevant cost implications.

RPO (Recovery-point-objective) indicates how much data loss is acceptable for the user/customer. This is determined by the log backup frequency. More frequent log backups indicate lower RPO and the minimum value supported by Azure Backup service is 15 minutes. So log backup frequency can be 15 minutes or higher.

RTO (Recovery-time-objective) indicates how fast the data should be restored to the last available point-in-time after a data loss scenario. This depends on the recovery strategy employed by HANA, which is usually dependent on how many files are required for restore. This has cost implications as well, and the following table should help in understanding all scenarios and their implications.

|Backup policy  |RTO  |Cost  |
|---------|---------|---------|
|Daily Full + logs     |   Fastest, since we need only one full copy + required logs for point-in-time restore      |    Costliest option since a full copy is taken daily and so more and more data is accumulated in backend until the retention time   |
|Weekly Full + daily differential + logs     |   Slower than the above option, but faster than the next option since we require one full copy + one differential copy + logs for point-in-time restore      |    Less expensive option since the daily differential is usually smaller than full and a full copy is taken only once a week      |
|Weekly Full + daily incremental + logs     |  Slowest since we need one full copy + 'n' incrementals + logs for point-in-time recovery       |     Least expensive option since the daily incremental will be smaller than differential and a full copy is taken only weekly    |

> [!NOTE]
> The above options are the most common, but not the only options. For example, you can have a weekly full backup + differentials twice a week + logs.

Therefore, you can select the policy variant based on RPO and RTO objectives and cost considerations.

### Impact of modifying a policy

A few principles should be kept in mind when determining the impact of switching a backup item's policy from Policy 1 (P1) to Policy 2 (P2) or of editing Policy 1 (P1).

- All changes are also applied retroactively. The latest backup policy is applied on the recovery points taken earlier as well. For example, assume that the daily full retention is 30 days and 10 recovery points were taken according to the currently active policy. If the daily full's retention is changed to 10 days, then the previous point's expiry time is also recalculated as start time + 10 days and deleted if they're expired.
- The scope of change also includes day of backup, type of backup along with retention. For example: If a policy is changed from daily full to weekly full on Sundays, all earlier fulls that aren't on Sundays will be marked for deletion.
- A parent isn't deleted until the child is active/not-expired. Every backup type has an expiration time according to the currently active policy. But a full backup type is considered as parent to subsequent 'differentials', 'incrementals' and 'logs'. A 'differential' and a 'log' aren't parents to anyone else. An 'incremental' can be a parent to subsequent 'incremental'. Even if a 'parent' is marked for deletion, it's not actually deleted if the child 'differentials' or 'logs' aren't expired. For example, if a policy is changed from daily full to weekly full on Sundays, all earlier fulls that aren't on Sundays will be marked for deletion. But they aren't actually deleted until the logs that were taken daily earlier are expired. In other words, they're retained according to the latest log duration. Once the logs expire, both the logs and these fulls will be deleted.

With these principles, you can read the following table to understand the implications of a policy change.

|Old policy/ New policy  |Daily fulls + logs  | Weekly fulls + daily differentials + logs  |Weekly fulls + daily incrementals + logs  |
|---------|---------|---------|---------|
|Daily fulls + logs     |   -      |    The previous fulls that aren't on the same day of the week are marked for deletion but kept until the log retention period     |    The previous fulls that aren't on the same day of the week are marked for deletion but kept until the log retention period     |
|Weekly fulls + daily differentials + logs     |   The previous weekly fulls retention is recalculated as per latest policy. The previous differentials are immediately deleted      |    -     |    The previous differentials are immediately deleted     |
|Weekly fulls + daily incrementals + logs     |     The previous weekly fulls retention is recalculated as per latest policy. The previous incrementals are immediately deleted    |     The previous incrementals are immediately deleted    |    -     |

## Next steps

Learn how to [back up SAP HANA databases](./backup-azure-sap-hana-database.md) running on Azure VMs.
