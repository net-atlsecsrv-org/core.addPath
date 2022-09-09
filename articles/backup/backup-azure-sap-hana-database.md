---
title: Back up an SAP HANA database to Azure with Azure Backup 
description: In this article, learn how to back up an SAP HANA database to Azure virtual machines with the Azure Backup service.
ms.topic: conceptual
ms.date: 11/12/2019
---

# Back up SAP HANA databases in Azure VMs

SAP HANA databases are critical workloads that require a low recovery-point objective (RPO) and long-term retention. You can back up SAP HANA databases running on Azure virtual machines (VMs) by using [Azure Backup](backup-overview.md).

This article shows how to back up SAP HANA databases that are running on Azure VMs to an Azure Backup Recovery Services vault.

In this article, you will learn how to:
> [!div class="checklist"]
>
> * Create and configure a vault
> * Discover databases
> * Configure backups
> * Run an on-demand backup job

## Prerequisites

Refer to the [prerequisites](tutorial-backup-sap-hana-db.md#prerequisites) and [setting up permissions](tutorial-backup-sap-hana-db.md#setting-up-permissions) sections to set up the database for backup.

### Set up network connectivity

For all operations, the SAP HANA VM needs connectivity to Azure public IP addresses. VM operations (database discovery, configure backups, schedule backups, restore recovery points, and so on) can't work without connectivity. Establish connectivity by allowing access to the Azure datacenter IP ranges:

* You can download the [IP address ranges](https://www.microsoft.com/download/details.aspx?id=41653) for Azure datacenters, and then allow access to these IP addresses.
* If you're using network security groups (NSGs), you can use the AzureCloud [service tag](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) to allow all Azure public IP addresses. You can use the [Set-AzureNetworkSecurityRule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)  to modify NSG rules.
* Port 443 should be added to the allow-list since the transport is via HTTPS.

## Onboard to the public preview

Onboard to the public preview as follows:

* In the portal, register your subscription ID to the Recovery Services service provider by [following this article](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* For 'Az' module in PowerShell, run this cmdlet . It should complete as "Registered".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* If you are using 'AzureRM' module in PowerShell, run this cmdlet. It should complete as "Registered".

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## Discover the databases

1. In the vault, in **Getting Started**, click **Backup**. In **Where is your workload running?**, select **SAP HANA in Azure VM**.
2. Click **Start Discovery**. This initiates discovery of unprotected Linux VMs in the vault region.

   * After discovery, unprotected VMs appear in the portal, listed by name and resource group.
   * If a VM isn't listed as expected, check whether it's already backed up in a vault.
   * Multiple VMs can have the same name but they belong to different resource groups.

3. In **Select Virtual Machines**, click the link to download the script that provides permissions for the Azure Backup service to access the SAP HANA VMs for database discovery.
4. Run the script on each VM hosting SAP HANA databases that you want to back up.
5. After running the script on the VMs, in **Select Virtual Machines**, select the VMs. Then click **Discover DBs**.
6. Azure Backup discovers all SAP HANA databases on the VM. During discovery, Azure Backup registers the VM with the vault, and installs an extension on the VM. No agent is installed on the database.

    ![Discover SAP HANA databases](./media/backup-azure-sap-hana-database/hana-discover.png)

## Configure backup  

Now enable backup.

1. In Step 2, click **Configure Backup**.

    ![Configure Backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. In **Select items to back up**, select all the databases you want to protect > **OK**.

    ![Select items to back up](./media/backup-azure-sap-hana-database/select-items.png)
3. In **Backup Policy** > **Choose backup policy**, create a new backup policy for the databases, in accordance with the instructions below.

    ![Choose backup policy](./media/backup-azure-sap-hana-database/backup-policy.png)
4. After creating the policy, on the **Backup** menu, click **Enable backup**.

    ![Enable backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Track the backup configuration progress in the **Notifications** area of the portal.

### Create a backup policy

A backup policy defines when backups are taken, and how long they're retained.

* A policy is created at the vault level.
* Multiple vaults can use the same backup policy, but you must apply the backup policy to each vault.

Specify the policy settings as follows:

1. In **Policy name**, enter a name for the new policy.

   ![Enter policy name](./media/backup-azure-sap-hana-database/policy-name.png)
2. In **Full Backup policy**, select a **Backup Frequency**, choose **Daily** or **Weekly**.
   * **Daily**: Select the hour and time zone in which the backup job begins.
       * You must run a full backup. You can't turn off this option.
       * Click **Full Backup** to view the policy.
       * You can't create differential backups for daily full backups.
   * **Weekly**: Select the day of the week, hour, and time zone in which the backup job runs.

   ![Select backup frequency](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. In **Retention Range**, configure retention settings for the full backup.
    * By default all options are selected. Clear any retention range limits you don't want to use, and set those that you do.
    * The minimum retention period for any type of backup (full/differential/log) is seven days.
    * Recovery points are tagged for retention based on their retention range. For example, if you select a daily full backup, only one full backup is triggered each day.
    * The backup for a specific day is tagged and retained based on the weekly retention range and setting.
    * The monthly and yearly retention ranges behave in a similar way.

4. In the **Full Backup policy** menu, click **OK** to accept the settings.
5. Select **Differential Backup** to add a differential policy.
6. In **Differential Backup policy**, select **Enable** to open the frequency and retention controls.
    * At most, you can trigger one differential backup per day.
    * Differential backups can be retained for a maximum of 180 days. If you need longer retention, you must use full backups.

    ![Differential backup policy](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Incremental backups aren't currently supported.

7. Click **OK** to save the policy and return to the main **Backup policy** menu.
8. Select **Log Backup** to add a transactional log backup policy,
    * In **Log Backup**, select **Enable**.  This cannot be disabled as SAP HANA manages all log backups.
    * Set the frequency and retention controls.

    > [!NOTE]
    > Log backups only begin to flow after a successful full backup is completed.

9. Click **OK** to save the policy and return to the main **Backup policy** menu.
10. After you finish defining the backup policy, click **OK**.

> [!NOTE]
> Each log backup is chained to the previous full backup to form a recovery chain. This full backup will be retained until the retention of the last log backup has expired. This might mean that the full backup is retained for an extra period to make sure all the logs can be recovered. Let’s assume user has a weekly full backup, daily differential and 2 hour logs. All of them are retained for 30 days. But, the weekly full can be really cleaned up/deleted only after the next full backup is available i.e., after 30 + 7 days. Say, a weekly full backup happens on Nov 16th. As per the retention policy, it should be retained until Dec 16th. The last log backup for this full happens before the next scheduled full, on Nov 22nd. Until this log is available until Dec 22nd, the Nov 16th full can't be deleted. So, the Nov 16th full is retained until Dec 22nd.

## Run an on-demand backup

Backups run in accordance with the policy schedule. You can run a backup on-demand as follows:

1. In the vault menu, click **Backup items**.
2. In **Backup Items**,  select the VM running the SAP HANA database, and then click **Backup now**.
3. In **Backup Now**, use the calendar control to select the last day that the recovery point should be retained. Then click **OK**.
4. Monitor the portal notifications. You can monitor the job progress in the vault dashboard > **Backup Jobs** > **In progress**. Depending on the size of your database, creating the initial backup may take a while.

## Run SAP HANA Studio backup on a database with Azure Backup enabled

If you want to take a local backup (using HANA Studio) of a database that's being backed up with Azure Backup, do the following:

1. Wait for any full or log backups for the database to finish. Check the status in SAP HANA Studio / Cockpit.
2. Disable log backups, and set the backup catalog to the file system for relevant database.
3. To do this, double-click **systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Set **enable_auto_log_backup** to **No**.
5. Set **log_backup_using_backint** to **False**.
6. Take an on-demand full backup of the database.
7. Wait for the full backup and catalog backup to finish.
8. Revert the previous settings back to those for Azure:
    * Set **enable_auto_log_backup** to **Yes**.
    * Set **log_backup_using_backint** to **True**.

## Next steps

* Learn how to [restore SAP HANA databases running on Azure VMs](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Learn how  to [manage SAP HANA databases that are backed up using Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
