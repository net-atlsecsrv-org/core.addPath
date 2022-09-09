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

The Backup Job menu will only show ad-hoc backup jobs. For scheduled jobs, use [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

### Are future databases automatically added for backup?

No, this isn't currently supported.

### If I delete a database from an instance, what will happen to the backups?

If a database is dropped from an SAP HANA instance, the database backups are still attempted. This implies that the deleted database begins to show up as unhealthy under **Backup Items** and is still protected.
The correct way to stop protecting this database is to perform **Stop Backup with delete data** on this database.

### If I change the name of the database after it has been protected, what will the behavior be?

A renamed database is treated as a new database. Therefore, the service will treat this situation as if the database weren't found and with fail the backups. The renamed database will appear as a new database and must be configured for protection.

### What are the prerequisites to back up SAP HANA databases on an Azure VM?

Refer to the [prerequisites](tutorial-backup-sap-hana-db.md#prerequisites) and [What the pre-registration script does](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) sections.

### What permissions should be set so Azure can back up SAP HANA databases?

Running the pre-registration script sets the required permissions to allow Azure to back up SAP HANA databases. You can find more what the pre-registration script does [here](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### Will backups work after migrating SAP HANA from SDC to MDC?

Refer to [this section](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) of the troubleshooting guide.

### Can Azure HANA Backup be set up against a virtual IP (load balancer) and not a virtual machine?

Currently we don't have the capability to set up the solution against a virtual IP alone. We need a virtual machine to execute the solution.

### How can I move an on-demand backup to the local file system instead of the Azure vault?

1. Wait for the currently running backup to complete on the desired database (check from studio for completion).
1. Disable log backups and set the catalog backup to **Filesystem** for the desired DB using the following steps:
1. Double-click **SYSTEMDB** -> **configuration** -> **Select Database** -> **Filter (log)**
    1. Set enable_auto_log_backup to **no**
    1. Set catalog_backup_using_backint to **false**
1. Take an on-demand backup (full / differential/ incremental) on the desired database, and wait for the backup and catalog backup to complete.
1. If you want to also move the log backups to the Filesystem, set enable_auto_log_backup to **yes**
1. Revert to the previous settings to allow backups to flow to the Azure vault:
    1. Set enable_auto_log_backup to **yes**
    1. Set catalog_backup_using_backint to **true**

>[!NOTE]
>Moving backups to the local Filesystem and switching back again to the Azure vault may cause a log chain break of the log backups in the vault. This will trigger a full backup, which once successfully completed will start backing up the logs.

### How can I use SAP HANA Backup with my HANA Replication set-up?

Currently, Azure Backup doesn't have the capability to understand an HSR set-up. This means that the primary and secondary nodes of the HSR will be treated as two individual, unrelated VMs. You'll first need to configure backup on the primary node. When a fail-over happens, backup must be configured on the secondary node (which now becomes the primary node). There's no automatic fail-over of backup to the other node.

To back up data from the active (primary) node at any given point in time, you can **switch protection**  to the secondary node, which has now become the primary after fail-over.

To perform this **switch protection**, follow these steps:

- [Stop protection](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (with retain data) on primary
- Run the [pre-registration script](https://aka.ms/scriptforpermsonhana) on the secondary node
- [Discover the databases](tutorial-backup-sap-hana-db.md#discover-the-databases) on the secondary node and [configure backups](tutorial-backup-sap-hana-db.md#configure-backup) on them

These steps need to be performed manually after every fail-over. You can perform these steps through command line / HTTP REST in addition to the Azure portal. To automate these steps, you can use an Azure runbook.

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

## Next steps

Learn how to [back up SAP HANA databases](./backup-azure-sap-hana-database.md) running on Azure VMs.
