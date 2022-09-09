---
title: Support for physical server assessment with Azure Migrate
description: Learn about support for physical server assessment with Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
---

# Support matrix for physical server assessment 

You can use the [Azure Migrate service](migrate-overview.md) to assess and migrate machines to the Microsoft Azure cloud. This article summarizes support settings and limitations for assessing and migrating on-premises physical servers.


## Overview

To assess on-premises machines for migration to Azure with this article, you add the Azure Migrate: Server Assessment tool to an Azure Migrate project. You deploy the [Azure Migrate appliance](migrate-appliance.md). The appliance continuously discovers on-premises machines, and sends configuration and performance data to Azure. After machine discovery, you gather discovered machines into groups, and run an assessment for a group

## Limitations

**Support** | **Details**
--- | ---
**Assessment limits**| Discover and assess up to 35,000 physical servers in a single [project](migrate-support-matrix.md#azure-migrate-projects).
**Project limits** | You can create multiple projects in an Azure subscription. A project can include VMware VMs, Hyper-V VMs, and physical servers, up to the assessment limits.
**Discovery** | The Azure Migrate appliance can discover up to 250 physical servers.
**Assessment** | You can add up to 35,000 machines in a single group.<br/><br/> You can assess up to 35,000 machines in a single assessment.

[Learn more](concepts-assessment-calculation.md) about assessments.




## Physical server requirements

| **Support**                | **Details**               
| :-------------------       | :------------------- |
| **Physical server deployment**       | The physical server can be standalone or deployed in a cluster. |
| **Permissions**           | **Windows:** Set up a local or domain user account on all the Windows servers that you want to include in the discovery. The user account needs to be added to these groups-Remote Desktop Users, Performance Monitor Users and Performance Log users. <br/> **Linux:** You need a root account on the Linux servers that you want to discover. |
| **Operating system** | All [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) and [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) operating systems are supported except the following:<br/> Windows Server 2003 <br/> SUSE Linux|


## Azure Migrate appliance requirements

Azure Migrate uses the [Azure Migrate appliance](migrate-appliance.md) for discovery and assessment. The appliance for physical servers can run on a VM or a physical machine. You set it up using a PowerShell script that you download from the Azure portal.

- Learn about [appliance requirements](migrate-appliance.md#appliance---physical) for physical servers.
- Learn about [URLs](migrate-appliance.md#url-access) the appliance needs to access.

## Port access

The following table summarizes port requirements for assessment.

**Device** | **Connection**
--- | ---
**Appliance** | Inbound connections on TCP port 3389 to allow remote desktop connections to the appliance.<br/> Inbound connections on port 44368 to remotely access the appliance management app using the URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Outbound connections on ports 443 (HTTPS), 5671 and 5672 (AMQP) to send discovery and performance metadata to Azure Migrate.
**Physical servers** | **Windows:** Inbound connections on WinRM ports 5985 (HTTP) and 5986 (HTTPS) to pull configuration and performance metadata from Windows servers. <br/> **Linux:**  Inbound connections on port 22 (UDP) to pull configuration and performance metadata from Linux servers. |

## Agent-based dependency visualization

[Dependency visualization](concepts-dependency-visualization.md) helps you to visualize dependencies across machines that you want to assess and migrate. For agent-based visualization, requirements and limitations are summarized in the following table.


**Requirement** | **Details**
--- | ---
**Deployment** | Before you deploy dependency visualization you should have an Azure Migrate project in place, with the Azure Migrate: Server Assessment tool added to the project. You deploy dependency visualization after setting up an Azure Migrate appliance to discover your on-premises machines.<br/><br/> Dependency visualization isn't available in Azure Government.
**Service Map** | Agent-based dependency visualization uses the [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) solution in [Azure Monitor logs](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> To deploy, you associate a new or existing Log Analytics workspace with an Azure Migrate project.
**Log Analytics workspace** | The workspace must be in the same subscription as the Azure Migrate project.<br/><br/> Azure Migrate supports workspaces residing in the East US, Southeast Asia and West Europe regions.<br/><br/>  The workspace must be in a region in which [Service Map is supported](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> The workspace for an Azure Migrate project can't be modified after it's added.
**Charges** | The Service Map solution doesn't incur any charges for the first 180 days (from the day that you associated the Log Analytics workspace with the Azure Migrate project).<br/><br/> After 180 days, standard Log Analytics charges will apply.<br/><br/> Using any solution other than Service Map in the associated Log Analytics workspace will incur standard Log Analytics charges.<br/><br/> If you delete the Azure Migrate project, the workspace isn't deleted with it. After deleting the project, Service Map isn't free, and each node will be charged as per the paid tier of Log Analytics workspace.
**Agents** | Agent-based dependency visualization requires two agents to be installed on each machine you want to analyze.<br/><br/> - [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> - [Dependency agent](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
**Internet connectivity** | If machines aren't connected to the internet, you need to install the Log Analytics gateway on them.

## Next steps

[Prepare for physical server assessment](tutorial-prepare-physical.md).
