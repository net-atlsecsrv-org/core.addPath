---
title: Azure Backup support matrix
description: Provides a summary of support settings and limitations for the Azure Backup service.

author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: dacurwin
---

# Support matrix for Azure Backup

You can use [Azure Backup](backup-overview.md) to back up data to the Microsoft Azure cloud platform. This article summarizes the general support settings and limitations for Azure Backup scenarios and deployments.

Other support matrices are available:

- Support matrix for [Azure virtual machine (VM) backup](backup-support-matrix-iaas.md)
- Support matrix for backup by using [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Support matrix for backup by using the [Microsoft Azure Recovery Services (MARS) agent](backup-support-matrix-mars-agent.md)

## Vault support

Azure Backup uses Recovery Services vaults to orchestrate and manage backups. It also uses vaults to store backed-up data.

The following table describes the features of Recovery Services vaults:

**Feature** | **Details**
--- | ---
**Vaults in subscription** | Up to 500 Recovery Services vaults in a single subscription.
**Machines in a vault** | Up to 1,000 Azure VMs in a single vault.<br/><br/> Up to 50 MABS servers can be registered in a single vault.
**Data sources in vault storage** | Maximum 54,400 GB. There's no limit for Azure VM backups.
**Backups to vault** | **Azure VMs:** Once a day.<br/><br/>**Machines protected by DPM/MABS:** Twice a day.<br/><br/> **Machines backed up directly by using the MARS agent:** Three times a day.
**Backups between vaults** | Backup is within a region.<br/><br/> You need a vault in every Azure region that contains VMs you want to back up. You can't back up to a different region.
**Move vaults** | You can [move vaults](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) across subscriptions or between resource groups in the same subscription.
**Move data between vaults** | Moving backed-up data between vaults isn't supported.
**Modify vault storage type** | You can modify the storage replication type (either geo-redundant storage or locally redundant storage) for a vault before backups are stored. After backups begin in the vault, the replication type can't be modified.

## On-premises backup support

Here's what's supported if you want to back up on-premises machines:

**Machine** | **What's backed up** | **Location** | **Features**
--- | --- | --- | ---
**Direct backup of Windows machine with MARS agent** | Files, folders, system state | Back up to Recovery Services vault. | Back up three times a day<br/><br/> No app-aware backup<br/><br/> Restore file, folder, volume
**Direct backup of Linux machine with MARS agent** | Backup not supported
**Back up to DPM** | Files, folders, volumes, system state, app data | Back up to local DPM storage. DPM then backs up to vault. | App-aware snapshots<br/><br/> Full granularity for backup and recovery<br/><br/> Linux supported for VMs (Hyper-V/VMware)<br/><br/> Oracle not supported
**Back up to MABS** | Files, folders, volumes, system state, app data | Back up to MABS local storage. MABS then backs up to the vault. | App-aware snapshots<br/><br/> Full granularity for backup and recovery<br/><br/> Linux supported for VMs (Hyper-V/VMware)<br/><br/> Oracle not supported

## Azure VM backup support

### Azure VM limits

**Limit** | **Details**
--- | ---
**Azure VM data disks** | Limit of 16
**Azure VM data disk size** | Supports backup of virtual machines with each disk size up to 30 TB and a maximum of 256 TB combined for all disks in a VM.

### Azure VM backup options

Here's what's supported if you want to back up Azure VMs:

**Machine** | **What's backed up** | **Location** | **Features**
--- | --- | --- | ---
**Azure VM backup by using VM extension** | Entire VM | Back up to vault. | Extension installed when you enable backup for a VM.<br/><br/> Back up once a day.<br/><br/> App-aware backup for Windows VMs; file-consistent backup for Linux VMs. You can configure app-consistency for Linux machines by using custom scripts.<br/><br/> Restore VM or disk.<br/><br/> Can't back up an Azure VM to an on-premises location.
**Azure VM backup by using MARS agent** | Files, folders, system state | Back up to vault. | Back up three times a day.<br/><br/> If you want to back up specific files or folders rather than the entire VM, the MARS agent can run alongside the VM extension.
**Azure VM with DPM** | Files, folders, volumes, system state, app data | Back up to local storage of Azure VM that's running DPM. DPM then backs up to vault. | App-aware snapshots.<br/><br/> Full granularity for backup and recovery.<br/><br/> Linux supported for VMs (Hyper-V/VMware).<br/><br/> Oracle not supported.
**Azure VM with MABS** | Files, folders, volumes, system state, app data | Back up to local storage of Azure VM that's running MABS. MABS then backs up to the vault. | App-aware snapshots.<br/><br/> Full granularity for backup and recovery.<br/><br/> Linux supported for VMs (Hyper-V/VMware).<br/><br/> Oracle not supported.

## Linux backup support

Here's what's supported if you want to back up Linux machines:

**Backup type** | **Linux (Azure endorsed)**
--- | ---
**Direct backup of on-premises machine that's running Linux** | Not supported. The MARS agent can be installed only on Windows machines.
**Using agent extension to back up Azure VM that's running Linux** | App-consistent backup by using [custom scripts](backup-azure-linux-app-consistent.md).<br/><br/> File-level recovery.<br/><br/> Restore by creating a VM from a recovery point or disk.
**Using DPM to back up on-premises or Azure VM that's running Linux** | File-consistent backup of Linux Guest VMs on Hyper-V and VMWare.<br/><br/> VM restoration of Hyper-V and VMWare Linux Guest VMs.<br/><br/> File-consistent backup not available for Azure VM.
**Using MABS to back up on-premises machine or Azure VM that's running Linux** | File-consistent backup of Linux Guest VMs on Hyper-V and VMWare.<br/><br/> VM restoration of Hyper-V and VMWare Linux guest VMs.<br/><br/> File-consistent backup not available for Azure VMs.

## Daylight saving time support

Azure Backup doesn't support automatic clock adjustment for daylight saving time for Azure VM backups. Modify backup policies manually as required.

## Disk deduplication support

Disk deduplication support is as follows:

- Disk deduplication is supported on-premises when you use DPM or MABs to back up Hyper-V VMs that are running Windows. Windows Server performs data deduplication (at the host level) on virtual hard disks (VHDs) that are attached to the VM as backup storage.
- Deduplication isn't supported in Azure for any Backup component. When DPM and MABS are deployed in Azure, the storage disks attached to the VM can't be deduplicated.

## Security and encryption support

Azure Backup supports encryption for in-transit and at-rest data.

### Network traffic to Azure

- Backup traffic from servers to the Recovery Services vault is encrypted by using Advanced Encryption Standard 256.
- Backup data is sent over a secure HTTPS link.
- Backup data is stored in the Recovery Services vault in encrypted form.
- Only you have the passphrase to unlock this data. Microsoft can't decrypt the backup data at any point.

    > [!WARNING]
    > After setting up the vault, only you have access to the encryption key. Microsoft never maintains a copy and doesn't have access to the key. If the key is misplaced, Microsoft can't recover the backup data.

### Data security

- When you're backing up Azure VMs, you need to set up encryption *within* the virtual machine.
- Azure Backup supports Azure Disk Encryption, which uses BitLocker on Windows virtual machines and **dm-crypt** on Linux virtual machines.
- On the back end, Azure Backup uses [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), which protects data at rest.

**Machine** | **In transit** | **At rest**
--- | --- | ---
**On-premises Windows machines without DPM/MABS** | ![Yes][green] | ![Yes][green]
**Azure VMs** | ![Yes][green] | ![Yes][green]
**On-premises Windows machines or Azure VMs with DPM** | ![Yes][green] | ![Yes][green]
**On-premises Windows machines or Azure VMs with MABS** | ![Yes][green] | ![Yes][green]

## Compression support

Backup supports the compression of backup traffic, as summarized in the following table.

- For Azure VMs, the VM extension reads the data directly from the Azure storage account over the storage network, so it isn't necessary to compress this traffic.
- If you're using DPM or MABS, you can save bandwidth by compressing the data before it's backed up.

**Machine** | **Compress to MABS/DPM (TCP)** | **Compress to vault (HTTPS)**
--- | --- | ---
**Direct backup of on-premises Windows machines** | NA | ![Yes][green]
**Backup of Azure VMs by using VM extension** | NA | NA
**Backup on on-premises/Azure machines by using MABS/DPM** | ![Yes][green] | ![Yes][green]

## Retention limits

**Setting** | **Limits**
--- | ---
**Max recovery points per protected instance (machine or workload)** | 9,999
**Max expiry time for a recovery point** | No limit
**Maximum backup frequency to DPM/MABS** | Every 15 minutes for SQL Server<br/><br/> Once an hour for other workloads
**Maximum backup frequency to vault** | **On-premises Windows machines or Azure VMs running MARS:** Three per day<br/><br/> **DPM/MABS:** Two per day<br/><br/> **Azure VM backup:** One per day
**Recovery point retention** | Daily, weekly, monthly, yearly
**Maximum retention period** | Depends on backup frequency
**Recovery points on DPM/MABS disk** | 64 for file servers; 448 for app servers <br/><br/>Unlimited tape recovery points for on-premises DPM

## Next steps

- [Review support matrix](backup-support-matrix-iaas.md) for Azure VM backup.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
