---
title: Run a disaster recovery drill for Azure VMs to a secondary Azure region with the Azure Site Recovery service
description: Learn how to run a disaster recovery drill for Azure VMs to a secondary Azure region for Azure IaaS VMs using the Azure Site Recovery service.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: raynew
ms.custom: mvc
---

# Run a disaster recovery drill for Azure VMs to a secondary Azure region

The [Azure Site Recovery](site-recovery-overview.md) service contributes to your business continuity and disaster recovery (BCDR) strategy by keeping your business apps up and running available during planned and unplanned outages. Site Recovery manages and orchestrates disaster recovery of on-premises machines and Azure virtual machines (VMs), including replication, failover, and recovery.

This tutorial shows you how to run a disaster recovery drill for an Azure VM, from one Azure region to another, with a test failover. A drill validates your replication strategy without data loss or downtime, and doesn't affect your production environment. In this tutorial, you learn how to:

> [!div class="checklist"]
> * Check the prerequisites
> * Run a test failover for a single VM

> [!NOTE]
> This tutorial helps you to perform a DR drill with minimal steps; in case you want to learn more about the various aspects associated with performing a DR drill, including networking considerations, automation or troubleshooting, refer to the documents under 'How To' for Azure VMs.

## Prerequisites

- Before you run a test failover, we recommend that you verify the VM properties to make sure everything's as expected.  Access the VM properties in **Replicated items**. The **Essentials** blade shows information about machines settings and status.
- **We recommend you use a separate Azure VM network for the test failover**, and not the default network that was set up when you enabled replication.
- Depending on your source networking configurations for each NIC, you can optionally specify **subnet, IP address, Public IP, Network Security Group or Internal Load Balancer** to attach to each NIC under test failover settings in Compute & Network prior to conducting DR drill.


## Run a test failover

1. In **Settings** > **Replicated Items**, click the VM **+Test Failover** icon.

2. In **Test Failover**, Select a recovery point to use for the failover:

    - **Latest**: Processes all the data in Site Recovery and provides the lowest RTO (Recovery Time Objective).
    - **Latest processed**: Fails the VM over to the latest recovery point that was processed by Site Recovery. The time stamp is shown. With this option, no time is spent processing
     data, so it provides a low RTO (Recovery Time Objective)
   - **Latest app-consistent**: This option fails over all VMs to the latest app-consistent recovery point. The time stamp is shown.
   - **Custom**: Fail over to particular recovery point. Custom is only available when you fail over a single VM, and not for failover with a recovery plan.

3. Select the target Azure virtual network to which Azure VMs in the secondary region will be
   connected, after the failover occurs.

    > [!NOTE]
    > Dropdown to select Azure virtual network will not be visible if the test failover settings are pre-configured for the replicated item.

4. To start the failover, click **OK**. To track progress, click the VM to open its properties. Or,
   you can click the **Test Failover** job in the vault name > **Settings** > **Jobs** > **Site
   Recovery jobs**.
5. After the failover finishes, the replica Azure VM appears in the Azure portal > **Virtual
   Machines**. Make sure that the VM is running, sized appropriately, and connected to the
   appropriate network.
6. To delete the VMs that were created during the test failover, click **Cleanup test failover** on
   the replicated item or the recovery plan. In **Notes**, record and save any observations
   associated with the test failover.

## Next steps

> [!div class="nextstepaction"]
> [Run a production failover](azure-to-azure-tutorial-failover-failback.md)
