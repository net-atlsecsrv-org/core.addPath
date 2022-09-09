---
title: Support for physical server assessment in Azure Migrate
description: Learn about support for physical server assessment with Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 03/23/2020
---

# Support matrix for physical server assessment 

This article summarizes prerequisites and support requirements when you assess physical servers for migration to Azure, using the [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool. If you want to migrate physical servers to Azure, review the [migration support matrix](migrate-support-matrix-physical-migration.md).


To assess physical servers, you create an Azure Migrate project, and add the Server Assessment tool to the project. After the tool is added, you deploy the [Azure Migrate appliance](migrate-appliance.md). The appliance continuously discovers on-premises machines, and sends machine metadata and performance data to Azure. After discovery is complete, you gather discovered machines into groups, and run an assessment for a group.


## Limitations

**Support** | **Details**
--- | ---
**Assessment limits** | You can discover and assess up to 35,000 physical servers in a single [Azure Migrate project](migrate-support-matrix.md#azure-migrate-projects).
**Project limits** | You can create multiple projects in an Azure subscription. In addition to physical servers, a project can include VMware VMs and Hyper-V VMs, up to the assessment limits for each.
**Discovery** | The Azure Migrate appliance can discover up to 250 physical servers.
**Assessment** | You can add up to 35,000 machines in a single group.<br/><br/> You can assess up to 35,000 machines in a single assessment.

[Learn more](concepts-assessment-calculation.md) about assessments.

## Physical server requirements

| **Support**                | **Details**               
| :-------------------       | :------------------- |
| **Physical server deployment**       | The physical server can be standalone, or deployed in a cluster. |
| **Permissions**           | **Windows:** You need a local or domain user account on all the Windows servers you want to discover. The user account should be added to these groups: Remote Desktop Users, Performance Monitor Users, and Performance Log users. <br/><br/> **Linux:** You need a root account on the Linux servers that you want to discover. |
| **Operating system** | All [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) and [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operating systems that are supported by Azure, except for Windows Server 2003, and SUSE Linux.|


## Azure Migrate appliance requirements

Azure Migrate uses the [Azure Migrate appliance](migrate-appliance.md) for discovery and assessment. The appliance for physical servers can run on a VM or a physical machine. You set the appliance up using a PowerShell script that you download from the Azure portal.

- Learn about [appliance requirements](migrate-appliance.md#appliance---physical) for physical servers.
- Learn about [URLs](migrate-appliance.md#url-access) the appliance needs to access.

## Port access

The following table summarizes port requirements for assessment.

**Device** | **Connection**
--- | ---
**Appliance** | Inbound connections on TCP port 3389, to allow remote desktop connections to the appliance.<br/><br/> Inbound connections on port 44368, to remotely access the appliance management app using the URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Outbound connections on ports 443 (HTTPS), to send discovery and performance metadata to Azure Migrate.
**Physical servers** | **Windows:** Inbound connections on WinRM ports 5985 (HTTP) and 5986 (HTTPS), to pull configuration and performance metadata from Windows servers. <br/><br/> **Linux:**  Inbound connections on port 22 (UDP), to pull configuration and performance metadata from Linux servers. |

## Agent-based dependency analysis requirements

[Dependency analysis](concepts-dependency-visualization.md) helps you to identify dependencies between on-premises machines that you want to assess and migrate to Azure. The table summarizes the requirements for setting up agent-based dependency analysis. Currently only agent-based dependency analysis is supported for physical servers.

**Requirement** | **Details** 
--- | --- 
**Before deployment** | You should have an Azure Migrate project in place, with the Server Assessment tool added to the project.<br/><br/>  You deploy dependency visualization after setting up an Azure Migrate appliance to discover your on-premises machines<br/><br/> [Learn how](create-manage-projects.md) to create a project for the first time.<br/> [Learn how](how-to-assess.md) to add an assessment tool to an existing project.<br/> Learn how to set up the Azure Migrate appliance for assessment of [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md), or physical servers.
**Azure Government** | Dependency visualization isn't available in Azure Government.
**Log Analytics** | Azure Migrate uses the [Service Map](../operations-management-suite/operations-management-suite-service-map.md) solution in [Azure Monitor logs](../log-analytics/log-analytics-overview.md) for dependency visualization.<br/><br/> You associate a new or existing Log Analytics workspace with an Azure Migrate project. The workspace for an Azure Migrate project can't be modified after it's added. <br/><br/> The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> The workspace must reside in the East US, Southeast Asia, or West Europe regions. Workspaces in other regions can't be associated with a project.<br/><br/> The workspace must be in a region in which [Service Map is supported](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).<br/><br/> In Log Analytics, the workspace associated with Azure Migrate is tagged with the Migration Project key, and the project name.
**Required agents** | On each machine you want to analyze, install the following agents:<br/><br/> The [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> The [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> If on-premises machines aren't connected to the internet, you need to download and install Log Analytics gateway on them.<br/><br/> Learn more about installing the [Dependency agent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) and [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics workspace** | The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> Azure Migrate supports workspaces residing in the East US, Southeast Asia, and West Europe regions.<br/><br/>  The workspace must be in a region in which [Service Map is supported](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> The workspace for an Azure Migrate project can't be modified after it's added.
**Costs** | The Service Map solution doesn't incur any charges for the first 180 days (from the day that you associate the Log Analytics workspace with the Azure Migrate project)/<br/><br/> After 180 days, standard Log Analytics charges will apply.<br/><br/> Using any solution other than Service Map in the associated Log Analytics workspace will incur [standard charges](https://azure.microsoft.com/pricing/details/log-analytics/) for Log Analytics.<br/><br/> When the Azure Migrate project is deleted, the workspace is not deleted along with it. After deleting the project, Service Map usage isn't free, and each node will be charged as per the paid tier of Log Analytics workspace/<br/><br/>If you have projects that you created before Azure Migrate general availability (GA- 28 February 2018), you might have incurred additional Service Map charges. To ensure payment after 180 days only, we recommend that you create a new project, since existing workspaces before GA are still chargeable.
**Management** | When you register agents to the workspace, you use the ID and key provided by the Azure Migrate project.<br/><br/> You can use the Log Analytics workspace outside Azure Migrate.<br/><br/> If you delete the associated Azure Migrate project, the workspace isn't deleted automatically. [Delete it manually](../azure-monitor/platform/manage-access.md).<br/><br/> Don't delete the workspace created by Azure Migrate, unless you delete the Azure Migrate project. If you do, the dependency visualization functionality will not work as expected.
**Internet connectivity** | If machines aren't connected to the internet, you need to install the Log Analytics gateway on them.

## Next steps

[Prepare for physical server assessment](tutorial-prepare-physical.md).
