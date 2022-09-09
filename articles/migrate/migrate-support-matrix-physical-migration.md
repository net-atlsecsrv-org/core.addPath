---
title: Support for physical server migration in Azure Migrate
description: Learn about support for physical server migration in Azure Migrate.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 06/14/2020
---

# Support matrix for physical server migration

This article summarizes support settings and limitations for migrating physical servers with [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) . If you're looking for information about assessing physical servers for migration to Azure, review the [assessment support matrix](migrate-support-matrix-physical.md).

## Migrating machines as physical

You can migrate on-premises machines as physical servers, using agent-based replication. Using this tool, you can migrate a wide range of machines to Azure:

- On-premises physical servers.
- VMs virtualized by platforms such as Xen, KVM.
- Hyper-V VMs or VMware VMs if for some reason you don't want to use the standard [Hyper-V](tutorial-migrate-hyper-v.md) or [VMware](server-migrate-overview.md) flows.
- VMs running in private clouds.
- VMs running in public clouds such as Amazon Web Services (AWS) or Google Cloud Platform (GCP).


## Migration limitations

You can select up to 10 machines at once for replication. If you want to migrate more machines, then replicate in groups of 10.


## Physical server requirements

The table summarizes support for physical servers you want to migrate using agent-based migration.

**Support** | **Details**
--- | ---
**Machine workload** | Azure Migrate supports migration of any workload (say Active Directory, SQL server, etc.) running on a supported machine.
**Operating systems** | For the latest information, review the [operating system support](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) for Site Recovery. Azure Migrate provides identical operating system support.
**Linux file system/guest storage** | For the latest information, review the [Linux file system support](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) for Site Recovery. Azure Migrate provides identical Linux file system support.
**Network/Storage** | For the latest information, review the [network](../site-recovery/vmware-physical-azure-support-matrix.md#network) and [storage](../site-recovery/vmware-physical-azure-support-matrix.md#storage) prerequisites for Site Recovery. Azure Migrate provides identical network/storage requirements.
**Azure requirements** | For the latest information, review the [Azure network](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [storage](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage), and [compute](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) requirements for Site Recovery. Azure Migrate has identical requirements for physical server migration.
**Mobility service** | The Mobility service agent must be installed on each machine you want to migrate.
**UEFI boot** | The migrated machine in Azure will be automatically converted to a BIOS boot Azure VM. Only server running Windows Server 2012 and later supported.<br/><br/> The OS disk should have up to four partitions, and volumes should be formatted with NTFS.
**UEFI - Secure boot**         | Not supported for migration.
**Target disk** | Machines can only be migrated to managed disks (standard HDD, standard SSD, premium SSD) in Azure.
**Disk size** | 2 TB OS disk; 8 TB for data disks.
**Disk limits** |  Up to 63 disks per machine.
**Encrypted disks/volumes** |  Machines with encrypted disks/volumes aren't supported for migration.
**Shared disk cluster** | Not supported.
**Independent disks** | Supported.
**Passthrough disks** | Supported.
**NFS** | NFS volumes mounted as volumes on the machines won't be replicated.
**iSCSI targets** | Machines with iSCSI targets aren't supported for agentless migration.
**Multipath IO** | Not supported.
**Storage vMotion** | Supported
**Teamed NICs** | Not supported.
**IPv6** | Not supported.



## Replication appliance requirements

If you set up the replication appliance manually on a physical server, then make sure that it complies with the requirements summarized in the table. When you set up the Azure Migrate replication appliance as an VMware VM using the OVA template provided in the Azure Migrate hub, the appliance is set up with Windows Server 2016, and complies with the support requirements. 

- Learn about [replication appliance requirements](migrate-replication-appliance.md#appliance-requirements).
- MySQL must be installed on the appliance. Learn about [installation options](migrate-replication-appliance.md#mysql-installation).
- Learn about [URLs](migrate-replication-appliance.md#url-access) the replication appliance needs to access.

## Azure VM requirements

All on-premises VMs replicated to Azure must meet the Azure VM requirements summarized in this table. When Site Recovery runs a prerequisites check for replication, the check will fail if some of the requirements aren't met.

**Component** | **Requirements** | **Details**
--- | --- | ---
Guest operating system | Verifies supported operating systems.<br/> You can migrate any workload running on a supported operating system. | Check fails if unsupported.
Guest operating system architecture | 64-bit. | Check fails if unsupported.
Operating system disk size | Up to 2,048 GB. | Check fails if unsupported.
Operating system disk count | 1 | Check fails if unsupported.
Data disk count | 64 or less. | Check fails if unsupported.
Data disk size | Up to 4,095 GB | Check fails if unsupported.
Network adapters | Multiple adapters are supported. |
Shared VHD | Not supported. | Check fails if unsupported.
FC disk | Not supported. | Check fails if unsupported.
BitLocker | Not supported. | BitLocker must be disabled before you enable replication for a machine.
VM name | From 1 to 63 characters.<br/> Restricted to letters, numbers, and hyphens.<br/><br/> The machine name must start and end with a letter or number. |  Update the value in the machine properties in Site Recovery.
Connect after migration-Windows | To connect to Azure VMs running Windows after migration:<br/> - Before migration enables RDP on the on-premises VM. Make sure that TCP, and UDP rules are added for the **Public** profile, and that RDP is allowed in **Windows Firewall** > **Allowed Apps**, for all profiles.<br/> For site-to-site VPN access, enable RDP and allow RDP in **Windows Firewall** -> **Allowed apps and features** for **Domain and Private** networks. In addition, check that the operating system's SAN policy is set to **OnlineAll**. [Learn more](prepare-for-migration.md). |
Connect after migration-Linux | To connect to Azure VMs after migration using SSH:<br/> Before migration, on the on-premises machine, check that the Secure Shell service is set to Start, and that firewall rules allow an SSH connection.<br/> After failover, on the Azure VM, allow incoming connections to the SSH port for the network security group rules on the failed over VM, and for the Azure subnet to which it's connected. In addition, add a public IP address for the VM. |  


## Next steps

[Migrate](tutorial-migrate-physical-virtual-machines.md) physical servers.
