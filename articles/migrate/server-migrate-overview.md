---
title: Select a VMware migration option with Azure Migrate Server Migration
description: Provides an overview of options for migrating VMware VMs to Azure with Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
---


# Select a VMware migration option

You can migrate VMware VMs to Azure using the Azure Migrate Server Migration tool. This tool offers a couple of options for VMware VM migration:

- Migration using agentless replication. Migrate VMs without needing to install anything on them.
- Migration with an agent for replication. Install an agent on the VM for replication.


## Compare migration methods

Use these selected comparisons to help you decide which method to use. You can also review full support requirements for [agentless](migrate-support-matrix-vmware-migration.md#agentless-migration) and [agent-based](migrate-support-matrix-vmware-migration.md#agent-based-migration) migration.

**Setting** | **Agentless** | **Agent-based**
--- | --- | ---
**Azure permissions** | You need permissions to create an Azure Migrate project, and to register Azure AD apps created when you deploy the Azure Migrate appliance. | You need Contributor permissions on the Azure subscription. 
**Replication** | A maximum of 300 VMs can be simultaneously replicated from a vCenter Server.<br/> If you have more than 50 VMs for migration, create multiple batches of VMs.<br/> Replicating more at a single time will impact performance.<br/><br/> In the portal, you can select up to 10 machines at once for replication. To replicate more machines, add in batches of 10.| Replication capacity increases by scaling the replication appliance.
**Appliance deployment** | The [Azure Migrate appliance](migrate-appliance.md) is deployed on-premises. | The [Azure Migrate Replication appliance](migrate-replication-appliance.md) is deployed on-premises.
**Site Recovery compatibility** | Compatible. | You can't replicate with Azure Migrate Server Migration if you've set up replication for a machine using Site Recovery.
**Target disk** | Managed disks | Managed disks
**Disk limits** | OS disk: 2 TB<br/><br/> Data disk: 32 TB<br/><br/> Maximum disks: 60 | OS disk: 2 TB<br/><br/> Data disk: 8 TB<br/><br/> Maximum disks: 63
**Passthrough disks** | Not supported | Supported
**UEFI boot** | Supported. | Supported.

## Compare deployment steps

After reviewing the limitations, understanding the steps involved in deploying each solution might help you decide which option to choose.

**Task** | **Details** |**Agentless** | **Agent-based**
--- | --- | --- | ---
**Deploy the Azure Migrate appliance** | A lightweight appliance that runs on a VMware VM.<br/><br/> The appliance is used to discover and assess machines, and to migrate machines using agentless migration. | Required.<br/><br/> If you've already set up the appliance for assessment,  you can use the same appliance for agentless migration. | Not required.<br/><br/> If you've set up an appliance for assessment, you can leave it in place, or remove it if you're done with assessment.
**Use the Server Assessment tool** | Assess machines with the Azure Migrate:Server Assessment tool. | You can assess machines before you migrate them, but you don't have to. | Assessment is optional | Assessment is optional.
**Use the Server Migration tool** | Add the Azure Migrate Server Migration tool in the Azure Migrate project. | Required | Required
**Prepare VMware for migration** | Configure settings on VMware servers and VMs. | Required | Required
**Install the Mobility service on VMs** | Mobility service runs on each VM you want to replicate | Not required | Required
**Deploy the replication appliance** | The [replication appliance](migrate-replication-appliance.md) is used for agent-based migration. It connects between the Mobility service running on VMs, and Server Migration. | Not required | Required
**Replicate VMs**. Enable VM replication. | Configure replication settings and select VMs to replicate | Required | Required
**Run a test migration** | Run a test migration to make sure everything's working as expected. | Required | Required
**Run a full migration** | Migrate the VMs. | Required | Required



## Next steps

[Migrate VMware VMs](tutorial-migrate-vmware.md) with agentless migration.



