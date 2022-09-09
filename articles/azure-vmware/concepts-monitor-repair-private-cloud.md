---
title: Concepts - Monitor and repair Azure VMware Solution private clouds
description: Learn how Azure VMware Solution monitors and repairs VMware ESXi servers on an Azure VMware Solution private cloud.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 11/20/2020
---

# Monitor and repair Azure VMware Solution private clouds

Azure VMware Solution continuously monitors the VMware ESXi servers on an Azure VMware Solution private cloud. 

## What Azure VMware Solution monitors

Azure VMware Solution monitors the following for failure conditions on the host:  

- Processor status 
- Memory status 
- Connection and power state 
- Hardware fan status 
- Network connectivity loss 
- Hardware system board status 
- Errors occurred on the disk(s) of a vSAN host 
- Hardware voltage 
- Hardware temperature status 
- Hardware power status 
- Storage status 
- Connection failure 

> [!NOTE]
> Azure VMware Solution tenant admins must not edit or delete the above defined VMware vCenter alarms, as these are managed by the Azure VMware Solution control plane on vCenter. These alarms are used by Azure VMware Solution monitoring to trigger the Azure VMware Solution host remediation process.

## Azure VMware Solution host remediation  

When Azure VMware Solution detects a degradation or failure on an Azure VMware Solution node on a tenant’s private cloud, it triggers the host remediation process. Host remediation involves replacing the faulty node with a new healthy node.  

The host remediation process starts by adding a new healthy node in the cluster. Then, when possible, the faulty host is placed in VMware vSphere maintenance mode. VMware vMotion is used to move the VMs off the faulty host to other available servers in the cluster, potentially allowing for zero downtime live migration of workloads. In scenarios where the faulty host can't be placed in maintenance mode, the host is removed from the cluster.

## Next steps

Here are some topics you may want to learn more about:

- [Azure VMware Solution private cloud upgrades](concepts-upgrades.md)
- [Lifecycle management of Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Protect your Azure VMware Solution VMs with Azure Security Center integration](azure-security-integration.md)
- [Back up Azure VMware Solution VMs with Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [Complete disaster recovery of virtual machines using Azure VMware Solution](disaster-recovery-for-virtual-machines.md)
