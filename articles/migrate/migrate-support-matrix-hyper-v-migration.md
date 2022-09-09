---
title: Support for Hyper-V migration in Azure Migrate
description: Learn about support for Hyper-V migration with Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
---

# Support matrix for Hyper-V migration

This article summarizes support settings and limitations for migrating Hyper-V VMs with [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . If you're looking for information about assessing Hyper-V VMs for migration to Azure, review the [assessment support matrix](migrate-support-matrix-hyper-v.md).

## Migration limitations

You can select up to 10 VMs at once for replication. If you want to migrate more machines, replicate in groups of 10.


## Hyper-V host requirements

| **Support**                | **Details**               
| :-------------------       | :------------------- |
| **Deployment**       | The Hyper-V host can be standalone or deployed in a cluster. <br/>Azure Migrate replication software (Hyper-V Replication provider) is installed on the Hyper-V hosts.|
| **Permissions**           | You need administrator permissions on the Hyper-V host. |
| **Host operating system** | Windows Server 2019, Windows Server 2016, or Windows Server 2012 R2 with latest updates. Note that Server core installation of these operating systems is also supported. |
| **Other Software requirements** | .NET Framework 4.7 or later |
| **Port access** |  Outbound connections on HTTPS port 443 to send VM replication data.
| **Free disk space (cache)** |  600 GB |
| **Free disk space (retention disk)** |  600 GB |


## Hyper-V VMs

| **Support**                  | **Details**               
| :----------------------------- | :------------------- |
| **Operating system** | All [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) and [Linux](../virtual-machines/linux/endorsed-distros.md) operating systems that are supported by Azure. |
**Windows Server 2003** | For VMs running Windows Server 2003, you need to [install Hyper-V Integration Services](prepare-windows-server-2003-migration.md) before migration. | 
**Linux VMs in Azure** | Some VMs might require changes so that they can run in Azure.<br/><br/> For Linux, Azure Migrate makes the changes automatically for these operating systems:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS  7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> For other operating systems you make the [required changes](prepare-for-migration.md#verify-required-changes-before-migrating) manually.
| **Required changes for Azure** | Some VMs might require changes so that they can run in Azure. Make adjustments manually before migration. The relevant articles contain instructions about how to do this. |
| **Linux boot**                 | If /boot is on a dedicated partition, it should reside on the OS disk, and not be spread across multiple disks.<br/> If /boot is part of the root (/) partition, then the '/' partition should be on the OS disk, and not span other disks. |
| **UEFI boot**                  | Supported. UEFI-based VMs will be migrated to Azure generation 2 VMs.  |
| **UEFI - Secure boot**         | Not supported for migration.|
| **Disk size**                  | 2 TB for the OS disk (BIOS boot), 4 TB for the OS disk (UEFI boot), 4 TB for the data disks.|
| **Disk number** | A maximum of 16 disks per VM.|
| **Encrypted disks/volumes**    | Not supported for migration.|
| **RDM/passthrough disks**      | Not supported for migration.|
| **Shared disk** | VMs using shared disks aren't supported for migration.|
| **NFS**                        | NFS volumes mounted as volumes on the VMs won't be replicated.|
| **ISCSI**                      | VMs with iSCSI targets aren't supported for migration.
| **Target disk**                | You can migrate to Azure VMs with managed disks only. |
| **IPv6** | Not supported.|
| **NIC teaming** | Not supported.|
| **Azure Site Recovery** | You can't replicate using Azure Migrate Server Migration if the VM is enabled for replication with Azure Site Recovery.|
| **Ports** | Outbound connections on HTTPS port 443 to send VM replication data.|

### URL access (public cloud)

The replication provider software on the Hyper-V hosts will need access to these URLs.

**URL** | **Details**
--- | ---
login.microsoftonline.com | Access control and identity management using Active Directory.
backup.windowsazure.com | Replication data transfer and coordination.
*.hypervrecoverymanager.windowsazure.com | Used for replication management.
*.blob.core.windows.net | Upload data to storage accounts. 
dc.services.visualstudio.com | Upload app logs used for internal monitoring.
time.windows.com | Verifies time synchronization between system and global time.

### URL access (Azure Government)

The replication provider software on the Hyper-V hosts will need access to these URLs.

**URL** | **Details**
--- | ---
login.microsoftonline.us | Access control and identity management using Active Directory.
backup.windowsazure.us | Replication data transfer and coordination.
*.hypervrecoverymanager.windowsazure.us | Used for replication management.
*.blob.core.usgovcloudapi.net | Upload data to storage accounts.
dc.services.visualstudio.com | Upload app logs used for internal monitoring.
time.nist.gov | Verifies time synchronization between system and global time.

## Azure VM requirements

All on-premises VMs replicated to Azure must meet the Azure VM requirements summarized in this table.

**Component** | **Requirements** | **Details**
--- | --- | ---
Operating system disk size | Up to 2,048 GB. | Check fails if unsupported.
Operating system disk count | 1 | Check fails if unsupported.
Data disk count | 16 or less. | Check fails if unsupported.
Data disk size | Up to 4,095 GB | Check fails if unsupported.
Network adapters | Multiple adapters are supported. |
Shared VHD | Not supported. | Check fails if unsupported.
FC disk | Not supported. | Check fails if unsupported.
BitLocker | Not supported. | BitLocker must be disabled before you enable replication for a machine.
VM name | From 1 to 63 characters.<br/> Restricted to letters, numbers, and hyphens.<br/><br/> The machine name must start and end with a letter or number. |  Update the value in the machine properties in Site Recovery.
Connect after migration-Windows | To connect to Azure VMs running Windows after migration:<br/><br/> - Before migration, enable RDP on the on-premises VM. Make sure that TCP, and UDP rules are added for the **Public** profile, and that RDP is allowed in **Windows Firewall** > **Allowed Apps**, for all profiles.<br/><br/> - For site-to-site VPN access, enable RDP and allow RDP in **Windows Firewall** -> **Allowed apps and features** for **Domain and Private** networks. In addition, check that the operating system's SAN policy is set to **OnlineAll**. [Learn more](prepare-for-migration.md). |
Connect after migration-Linux | To connect to Azure VMs after migration using SSH:<br/><br/> - Before migration, on the on-premises machine, check that the Secure Shell service is set to Start, and that firewall rules allow an SSH connection.<br/><br/> - After migration, on the Azure VM, allow incoming connections to the SSH port for the network security group rules on the failed over VM, and for the Azure subnet to which it's connected. In addition, add a public IP address for the VM. |  

## Next steps

[Migrate Hyper-V VMs](tutorial-migrate-hyper-v.md) for migration.
