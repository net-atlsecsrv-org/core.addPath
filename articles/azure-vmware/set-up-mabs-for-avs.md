---
title: Set up Azure Backup Server for Azure VMware Solution
description: Set up your Azure VMware Solution environment to back up virtual machines by using Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
---

# Set up Azure Backup Server for Azure VMware Solution

Azure Backup Server is a robust enterprise backup and recovery system that contributes to your business continuity and disaster recovery (BCDR) strategy. During the Azure VMware Solution preview, you can configure only virtual machine (VM)-level backup by using Azure Backup Server. 

Azure Backup Server can store backup data to:

- **Disk**: For short-term storage, Azure Backup Server backs up data to disk pools.
- **Azure**: For both short-term and long-term storage off-premises, Azure Backup Server data stored in disk pools can be backed up to the Microsoft Azure cloud by using Azure Backup.

When outages occur and source data is unavailable, you can use Azure Backup Server to restore data to the source or an alternate location easily. That way, if the original data is unavailable because of planned or unexpected issues, you can easily restore data to an alternate location.

In this article, we help you prepare your Azure VMware Solution environment to back up VMs by using Azure Backup Server. We walk you through steps to: 

> [!div class="checklist"]
> * Determine the recommended VM disk type and size to use.
> * Create a Recovery Services vault that stores the recovery points.
> * Set the storage replication for a Recovery Services vault.
> * Add storage to Azure Backup Server.

## Supported VMware features

- **Agentless backup:** Azure Backup Server doesn't require an agent to be installed on the vCenter or ESXi server to back up the virtual machine. Instead, just provide the IP address or fully qualified domain name (FQDN) and the sign-in credentials used to authenticate the VMware server with Azure Backup Server.
- **Cloud-integrated backup:** Azure Backup Server protects workloads to disk and the cloud. The backup and recovery workflow of Azure Backup Server helps you manage long-term retention and offsite backup.
- **Detect and protect VMs managed by vCenter:** Azure Backup Server detects and protects VMs deployed on a vCenter or ESXi server. Azure Backup Server also detects VMs managed by vCenter so that you can protect large deployments.
- **Folder-level autoprotection:** vCenter lets you organize your VMs in VM folders. Azure Backup Server detects these folders, and you can use it to protect VMs at the folder level, which includes all subfolders. When protecting folders, Azure Backup Server not only protects the VMs in that folder but also protects VMs added later. Azure Backup Server detects new VMs daily and protects them automatically. As you organize your VMs in recursive folders, Azure Backup Server automatically detects and protects the new VMs deployed in the recursive folders.
- **Azure Backup Server continues to protect vMotioned VMs within the cluster:** As VMs are vMotioned for load balancing within the cluster, Azure Backup Server automatically detects and continues VM protection.
- **Recover necessary files faster:** Azure Backup Server can recover files or folders from a Windows VM without recovering the entire VM.

## Limitations

- Update Rollup 1 for Azure Backup Server v3 must be installed.
- You can't back up user snapshots before the first Azure Backup Server backup. After Azure Backup Server finishes the first backup, then you can back up user snapshots.
- Azure Backup Server can't protect VMware VMs with pass-through disks and physical raw device mappings (pRDMs).
- Azure Backup Server can't detect or protect VMware vApps.

To set up Azure Backup Server for Azure VMware Solution, you must finish the following steps:

- Set up the prerequisites and environment.
- Create a Recovery Services vault.
- Download and install Azure Backup Server.
- Add storage to Azure Backup Server.

### Deployment architecture

Azure Backup Server is deployed as an Azure infrastructure as a service (IaaS) VM to protect Azure VMware Solution VMs.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Azure Backup Server is deployed as an Azure infrastructure as a service (IaaS) VM to protect Azure VMware Solution VMs." border="false":::

## Prerequisites for the Azure Backup Server environment

Consider the recommendations in this section when you install Azure Backup Server in your Azure environment.

### Azure Virtual Network

Ensure that you [configure networking for your VMware private cloud in Azure](tutorial-configure-networking.md).

### Determine the size of the virtual machine

You must create a Windows virtual machine in the virtual network that you created in the previous step. When you choose a server for running Azure Backup Server, start with a gallery image of Windows Server 2019 Datacenter. The tutorial [Create your first Windows virtual machine in the Azure portal](../virtual-machines/windows/quick-create-portal.md) gets you started with the recommended VM in Azure, even if you've never used Azure.

The following table summarizes the maximum number of protected workloads for each Azure Backup Server virtual machine size. The information is based on internal performance and scale tests with canonical values for the workload size and churn. The actual workload size can be larger but should be accommodated by the disks attached to the Azure Backup Server virtual machine.

| Maximum protected workloads | Average workload size | Average workload churn (daily) | Minimum storage IOPS | Recommended disk type/size      | Recommended VM size |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Net 5% churn                   | 2,000             | Standard HDD (8 TB or above size per disk)  | A4V2       |
| 40                      | 150 GB                | Net 10% churn                  | 4,500             | Premium SSD* (1 TB or above size per disk) | DS3_V2     |
| 60                      | 200 GB                | Net 10% churn                  | 10,500            | Premium SSD* (8 TB or above size per disk) | DS3_V2     |

*To get the required IOPs, use minimum recommended- or higher-size disks. Smaller-size disks offer lower IOPs.

> [!NOTE]
> Azure Backup Server is designed to run on a dedicated, single-purpose server. You can't install Azure Backup Server on a computer that:
> * Runs as a domain controller.
> * Has the Application Server role installed.
> * Is a System Center Operations Manager management server.
> * Runs Exchange Server.
> * Is a node of a cluster.

### Disks and storage

Azure Backup Server requires disks for installation, which includes system files, installation files, prerequisite software, database files, and dedicated disks for the storage pool.

| Requirement                      | Recommended size  |
|----------------------------------|-------------------------|
| Azure Backup Server installation                | Installation location: 3 GB<br />Database files drive: 900 MB<br />System drive: 1 GB for SQL Server installation<br /><br />You'll also need space for Azure Backup Server to copy the file catalog to a temporary installation location when you archive.      |
| Disk for storage pool<br />(Uses basic volumes, can't be on a dynamic disk) | Two to three times the size of the protected data.<br />For detailed storage calculation, see [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

To learn how to attach a new managed data disk to an existing Azure VM, see [Attach a managed data disk to a Windows VM by using the Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> A single Azure Backup Server has a soft limit of 120 TB for the storage pool.

### Store backup data on local disk and in Azure

Storing backup data in Azure reduces backup infrastructure on the Azure Backup Server VM. For operational recovery (backup), Azure Backup Server stores backup data on Azure disks attached to the VM. After the disks and storage space are attached to the VM, Azure Backup Server manages the storage for you. The amount of backup data storage depends on the number and size of disks attached to each Azure VM. Each size of the Azure VM has a maximum number of disks that can be attached. For example, A2 is four disks, A3 is eight disks, and A4 is 16 disks. Again, the size and number of disks determine the total backup storage pool capacity.

> [!IMPORTANT]
> You should *not* retain operational recovery data on Azure Backup Server-attached disks for more than five days. If data is more than five days old, store it in a Recovery Services vault.

To store backup data in Azure, create or use a Recovery Services vault. When you prepare to back up the Azure Backup Server workload, you [configure the Recovery Services vault](#create-a-recovery-services-vault). Once configured, each time an online backup job runs, a recovery point gets created in the vault. Each Recovery Services vault holds up to 9,999 recovery points. Depending on the number of recovery points created, and how long they're retained, you can retain backup data for many years. For example, you could create monthly recovery points and retain them for five years.

> [!IMPORTANT]
> Whether you send backup data to Azure or keep it locally, you must register Azure Backup Server with a Recovery Services vault.

### Scale deployment

If you want to scale your deployment, you have the following options:

- **Scale up**: Increase the size of the Azure Backup Server VM from A series to DS3 series, and increase the local storage.
- **Offload data**: Send older data to Azure and retain only the newest data on the storage attached to the Azure Backup Server machine.
- **Scale out**: Add more Azure Backup Server machines to protect the workloads.

### .NET Framework

The VM must have .NET Framework 3.5 SP1 or higher installed.

### Join a domain

The Azure Backup Server VM must be joined to a domain, and a domain user with administrator privileges on the VM must install Azure Backup Server.

Though not supported at the time of preview, Azure Backup Server deployed in an Azure VM can back up workloads on the VMs in Azure VMware Solution. The workloads should be in the same domain to enable the backup operation.

## Create a Recovery Services vault

A Recovery Services vault is a storage entity that stores the recovery points created over time. It also contains backup policies that are associated with protected items.

1. Sign in to your subscription in the [Azure portal](https://portal.azure.com/).

1. On the left menu, select **All services**.

   ![On the left menu, select All services.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. In the **All services** dialog box, enter **Recovery Services** and select **Recovery Services vaults** from the list.

   ![Enter and choose Recovery Services vaults.](../backup/media/backup-create-rs-vault/all-services.png)

   The list of Recovery Services vaults in the subscription appears.

1. On the **Recovery Services vaults** dashboard, select **Add**.

   ![Add a Recovery Services vault.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   The **Recovery Services vault** dialog box opens.

1. Enter values for the **Name**, **Subscription**, **Resource group**, and **Location**.

   ![Configure the Recovery Services vault.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: Enter a friendly name to identify the vault. The name must be unique to the Azure subscription. Specify a name that has at least two but not more than 50 characters. The name must start with a letter and consist only of letters, numbers, and hyphens.
   - **Subscription**: Choose the subscription to use. If you're a member of only one subscription, you'll see that name. If you're not sure which subscription to use, use the default (suggested) subscription. There are multiple choices only if your work or school account is associated with more than one Azure subscription.
   - **Resource group**: Use an existing resource group or create a new one. To see the list of available resource groups in your subscription, select **Use existing**, and then select a resource from the drop-down list. To create a new resource group, select **Create new** and enter the name.
   - **Location**: Select the geographic region for the vault. To create a vault to protect Azure VMware Solution virtual machines, the vault *must* be in the same region as the Azure VMware Solution private cloud.

1. When you're ready to create the Recovery Services vault, select **Create**.

   ![Create the Recovery Services vault.](../backup/media/backup-create-rs-vault/click-create-button.png)

   It can take a while to create the Recovery Services vault. Monitor the status notifications in the **Notifications** area in the upper-right corner of the portal. After your vault is created, it's visible in the list of Recovery Services vaults. If you don't see your vault, select **Refresh**.

   ![Refresh the list of backup vaults.](../backup/media/backup-create-rs-vault/refresh-button.png)

## Set storage replication

The storage replication option lets you choose between geo-redundant storage (the default) and locally redundant storage. Geo-redundant storage copies the data in your storage account to a secondary region, which makes your data durable. Locally redundant storage is a cheaper option that isn't as durable. To learn more about geo-redundant and locally redundant storage options, see [Azure Storage redundancy](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Changing the setting of **Storage replication type Locally-redundant/Geo-redundant** for a Recovery Services vault must be done before you configure backups in the vault. After you configure backups, the option to modify it is disabled, and you can't change the storage replication type.

1. From **Recovery Services vaults**, select the new vault. 

1. Under **Settings**, select **Properties**. Under **Backup Configuration**, select **Update**.

1. Select the storage replication type, and select **Save**.

   ![Set storage configuration for new vault.](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## Download and install the software package

Follow the steps in this section to download, extract, and install the software package.

### Download the software package

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. If you already have a Recovery Services vault open, continue to the next step. If you don't have a Recovery Services vault open but you're in the Azure portal, on the main menu, select **Browse**.

   1. In the list of resources, enter **Recovery Services**.

   1. As you begin typing, the list filters based on your input. When you see **Recovery Services vaults**, select it.

   ![Create Recovery Services vault step 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. From the list of Recovery Services vaults, select a vault.

   The selected vault dashboard opens.

   ![The selected vault dashboard opens.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   The **Settings** option opens by default. If closed, select **Settings** to open it.

   ![The Settings option opens by default. If closed, select Settings to open it.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Select **Backup** to open the **Getting Started** wizard.

   ![Select Backup to open the Getting Started wizard.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. In the window that opens, do the following:

   1. From the **Where is your workload running?** menu, select **On-Premises**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Where is your workload running?":::

   1. From the **What do you want to back up?** menu, select the workloads you want to protect by using Azure Backup Server.

   1. Select **Prepare Infrastructure** to download and install Azure Backup Server and the vault credentials.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Prepare Infrastructure":::

1. In the **Prepare infrastructure** window that opens, do the following:

   1. Select the **Download** link to install Azure Backup Server.

   1. Download the vault credentials by selecting the **Already downloaded or using the latest Azure Backup Server installation** check box, and then select **Download**. You use the vault credentials during the registration of Azure Backup Server to the Recovery Services vault. The links take you to the Download Center, where you download the software package.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Prepare Infrastructure - Azure Backup Server":::

1. On the download page, select all the files and select **Next**.

   > [!NOTE]
   > You must download all the files to the same folder. Because the download size of the files together is greater than 3 GB, it might take up to 60 minutes for the download to complete. 

   ![On the download page, select all the files and select Next.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### Extract the software package

If you downloaded the software package to a different server, copy the files to the virtual machine that you created to deploy Azure Backup Server.

> [!WARNING]
> At least 4 GB of free space is required to extract the setup files.

1. After you've downloaded all the files, double-click **MicrosoftAzureBackupInstaller.exe** to open the **Microsoft Azure Backup** setup wizard, and then select **Next**.

1. Select the location to extract the files to, and select **Next**.

1. Select **Extract** to begin the extraction process.

   ![Select Extract to begin the extraction process.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Once extracted, select the option to **Execute setup.exe** and then select **Finish**.

> [!TIP]
> You can also locate the setup.exe file from the folder where you extracted the software package.

### Install the software package

1. On the setup window under **Install**, select **Microsoft Azure Backup** to open the setup wizard.

   ![On the setup window under Install, select Microsoft Azure Backup to open the setup wizard.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. On the **Welcome** screen, select **Next** to continue to the **Prerequisite Checks** page.

1. Select **Check Again** to determine if the hardware and software prerequisites for Azure Backup Server are met. If met successfully, select **Next**.

   ![ Select Check Again to determine if the hardware and software prerequisites for Azure Backup Server are met. If met successfully, select Next.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. The Azure Backup Server installation package comes bundled with the appropriate SQL Server binaries that are needed. When you start a new Azure Backup Server installation, select the **Install new Instance of SQL Server with this Setup** option. Then select **Check and Install**.

   ![The Azure Backup Server installation package comes bundled with the appropriate SQL Server binaries that are needed.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > If you want to use your own SQL Server instance, the supported SQL Server versions are SQL Server 2014 SP1 or higher, 2016, and 2017. All SQL Server versions should be Standard or Enterprise 64-bit. Azure Backup Server doesn't work with a remote SQL Server instance. The instance used by Azure Backup Server must be local. If you use an existing SQL Server instance for Azure Backup Server, the setup only supports the use of *named instances* of SQL Server.

   If a failure occurs with a recommendation to restart the machine, do so and select **Check Again**. If there are any SQL Server configuration issues, reconfigure SQL Server according to the SQL Server guidelines. Then retry to install or upgrade Azure Backup Server by using the existing instance of SQL Server.

   **Manual configuration**

   When you use your own instance of SQL Server, make sure you add builtin\Administrators to the sysadmin role to the master database.

   **SSRS configuration with SQL Server 2017**

   When you use your own instance of SQL Server 2017, you must configure SQL Server 2017 Reporting Services (SSRS) manually. After SSRS configuration, ensure that the **IsInitialized** property of SSRS is set to **True**. When this property is set to **True**, Azure Backup Server assumes that SSRS is already configured and skips the SSRS configuration.

   To check the SSRS configuration status, run the following command:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Use the following values for SSRS configuration:

   * **Service Account**: **Use built-in account** should be **Network Service**.
   * **Web Service URL**: **Virtual Directory** should be **ReportServer_\<SQLInstanceName>**.
   * **Database**: **DatabaseName** should be **ReportServer$\<SQLInstanceName>**.
   * **Web Portal URL**: **Virtual Directory** should be **Reports_\<SQLInstanceName>**.

   [Learn more](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) about SSRS configuration.

   > [!NOTE]
   > [Microsoft Online Services Terms](https://www.microsoft.com/licensing/product-licensing/products) (OST) governs the licensing for SQL Server used as the database for Azure Backup Server. According to OST, SQL Server bundled with Azure Backup Server can be used only as the database for Azure Backup Server.

1. After the installation is successful, select **Next**.

1. Provide a location for the installation of Microsoft Azure Backup Server files, and select **Next**.

   > [!NOTE]
   > The scratch location is required for backup to Azure. Ensure the scratch location is at least 5% of the data planned to be backed up to the cloud. For disk protection, separate disks need to be configured after the installation is finished. For more information about storage pools, see [Configure storage pools and disk storage](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Provide a location for the installation of Microsoft Azure Backup Server files, and select Next.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Provide a strong password for restricted local user accounts, and select **Next**.

   ![Provide a strong password for restricted local user accounts, and select Next.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Select whether you want to use Microsoft Update to check for updates, and select **Next**.

   > [!NOTE]
   > We recommend having Windows Update redirect to Microsoft Update, which offers security and important updates for Windows and other products like Azure Backup Server.

   ![Select whether you want to use Microsoft Update to check for updates, and select Next.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Review the **Summary of Settings**, and select **Install**.

   The installation happens in phases. The first phase installs the Microsoft Azure Recovery Services Agent, and the second phase checks for internet connectivity. If internet connectivity is available, you can continue with the installation. If not, you must provide proxy details to connect to the internet. The final phase checks the prerequisite software. If it's not installed, any missing software gets installed along with the Microsoft Azure Recovery Services Agent.

1. Select **Browse** to locate your vault credentials to register the machine to the Recovery Services vault, and then select **Next**.

1. Choose a pass phrase to encrypt or decrypt the data sent between Azure and your premises.

   > [!TIP]
   > You can automatically generate a passphrase or provide your own minimum 16-character passphrase.

1. Enter the location to save the pass phrase, and then select **Next** to register the server.

   > [!IMPORTANT]
   > Save the pass phrase to a safe location other than the local server. We strongly recommend using Azure Key Vault to store the pass phrase.

   After the Microsoft Azure Recovery Services Agent setup finishes, the installation step moves on to the installation and configuration of SQL Server and the Azure Backup Server components.

   ![After the Microsoft Azure Recovery Services Agent setup finishes, the installation step moves on to the installation and configuration of SQL Server and the Azure Backup Server components.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. After the installation step finishes, select **Close**.

### Install Update Rollup 1

Installation of Update Rollup 1 for Azure Backup Server v3 is mandatory before you can protect the workloads. To view the list of bug fixes and the installation instructions for Azure Backup Server v3 Update Rollup 1, see the Knowledge Base article [4534062](https://support.microsoft.com/en-us/help/4534062/).

## Add storage to Azure Backup Server

Azure Backup Server v3 supports Modern Backup Storage that offers:

-  Storage savings of 50%.
-  Backups that are three times faster.
-  More efficient storage.
-  Workload-aware storage.

### Volumes in Azure Backup Server

Add the data disks with the required storage capacity to the Azure Backup Server virtual machine if not already added.

Azure Backup Server v3 only accepts storage volumes. When you add a volume, Azure Backup Server formats the volume to Resilient File System (ReFS), which Modern Backup Storage requires.

### Add volumes to Azure Backup Server disk storage

1. In the **Management** pane, rescan the storage and then select **Add**. 

1. Select from the available volumes to add to the storage pool. 

1. After you add the available volumes, give them a friendly name to help you manage them. 

1. Select **OK** to format these volumes to ReFS so that Azure Backup Server can use the benefits of Modern Backup Storage.

![Add available volumes](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## Next steps

Continue to the next tutorial to learn how to configure backup of VMware VMs running on Azure VMware Solution by using Azure Backup Server.

> [!div class="nextstepaction"]
> [Configure backup of Azure VMware Solution VMs](backup-avs-vms-with-mabs.md)
