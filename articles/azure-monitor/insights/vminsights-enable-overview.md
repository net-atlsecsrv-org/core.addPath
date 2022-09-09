---
title: Enable Azure Monitor for VMs overview
description: Learn how to deploy and configure Azure Monitor for VMs. Find out the system requirements.
ms.subservice: 
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions

---

# Enable Azure Monitor for VMs overview

This article provides an overview of the options available to enable Azure Monitor for VMs to monitor health and performance of the following:

- Azure virtual machines 
- Azure virtual machine scale sets
- Hybrid virtual machines connected with Azure Arc
- On-premises virtual machines
- Virtual machines hosted in another cloud environment.  

To set up Azure Monitor for VMs:

* Enable a single Azure virtual machine, Azure virtual machine scale set, or Azure Arc machine by selecting **Insights** directly from their menu in the Azure portal.
* Enable multiple Azure virtual machines, Azure virtual machines, or Azure Arc machines by using Azure Policy. This method ensures that on existing and new VMs and scale sets, the required dependencies are installed and properly configured. Noncompliant virtual machines and scale sets are reported, so you can decide whether to enable them and to remediate them.
* Enable multiple Azure virtual machines, Azure Arc virtual machines, Azure virtual machine scale sets, or Azure Arc machines across a specified subscription or resource group by using PowerShell.
* Enable Azure Monitor for VMs to monitor VMs or physical computers hosted in your corporate network or other cloud environment.

## Supported machines
Azure Monitor for VMs supports the following machines:

- Azure virtual machine
- Azure virtual machine scale set
- Hybrid virtual machine connected with Azure Arc


## Supported Azure Arc machines
Azure Monitor for VMs is available for Azure Arc enabled servers in regions where the Arc extension service is available. You must be running version 0.9 or above of the Arc Agent.

| Connected source | Supported | Description |
|:--|:--|:--|
| Windows agents | Yes | Along with the [Log Analytics agent for Windows](../platform/log-analytics-agent.md), Windows agents need the Dependency agent. For more information, see [supported operating systems](../platform/agents-overview.md#supported-operating-systems). |
| Linux agents | Yes | Along with the [Log Analytics agent for Linux](../platform/log-analytics-agent.md), Linux agents need the Dependency agent. For more information, see [supported operating systems](#supported-operating-systems). |
| System Center Operations Manager management group | No | |

## Supported operating systems

Azure Monitor for VMs supports any operating system that supports the Log Analytics agent and Dependency agent. See [Overview of Azure Monitor agents
](../platform/agents-overview.md#supported-operating-systems) for a complete list.

See the following list of considerations on Linux support of the Dependency agent that supports Azure Monitor for VMs:

- Only default and SMP Linux kernel releases are supported.
- Nonstandard kernel releases, such as Physical Address Extension (PAE) and Xen, aren't supported for any Linux distribution. For example, a system with the release string of *2.6.16.21-0.8-xen* isn't supported.
- Custom kernels, including recompilations of standard kernels, aren't supported.
- For Debian distros other than version 9.4, the map feature isn't supported, and the Performance feature is available only from the Azure Monitor menu. It isn't available directly from the left pane of the Azure VM.
- CentOSPlus kernel is supported.
- The Linux kernel must be patched for the Spectre vulnerability. Please consult your Linux distribution vendor for more details.
## Log Analytics workspace
Azure Monitor for VMs requires a Log Analytics workspace. See [Configure Log Analytics workspace for Azure Monitor for VMs](vminsights-configure-workspace.md) for details and requirements of this workspace.
## Agents
Azure Monitor for VMs requires the following two agents to be installed on each virtual machine or virtual machine scale set to be monitored. Installing these agents and connecting them to the workspace is the only requirement to onboard the resource.

- [Log Analytics agent](../platform/log-analytics-agent.md). Collects events and performance data from the virtual machine or virtual machine scale set and delivers it to the Log Analytics workspace. Deployment methods for the Log Analytics agent on Azure resources use the VM extension for [Windows](../../virtual-machines/extensions/oms-windows.md) and [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency agent. Collects discovered data about processes running on the virtual machine and external process dependencies, which are used by the [Map feature in Azure Monitor for VMs](vminsights-maps.md). The Dependency agent relies on the Log Analytics agent to deliver its data to Azure Monitor. Deployment methods for the Dependency agent on Azure resources use the VM extension for [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) and [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> The Log Analytics agent is the same agent used by System Center Operations Manager. Azure Monitor for VMs can monitor agents that are also monitored by Operations Manager if they are directly connected, and you install the Dependency agent on them. Agents connected to Azure Monitor through a [management group connection](../tform/../platform/om-agents.md) cannot be monitored by Azure Monitor for VMs.

The following are multiple methods for deploying these agents. 

| Method | Description |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Install both agents on a single virtual machine, virtual machine scale set, or hybrid virtual machines connected with Azure Arc. |
| [Resource Manager templates](vminsights-enable-resource-manager.md) | Install both agents using any of the supported methods to deploy a Resource Manager template including CLI and PowerShell. |
| [Azure Policy](./vminsights-enable-policy.md) | Assign  Azure Policy initiative to automatically install the agents when a virtual machine or virtual machine scale set is created. |
| [Manual install](./vminsights-enable-hybrid.md) | Install the agents in the guest operating system on computers hosted outside of Azure including in your datacenter or other cloud environments. |




## Management packs
When a Log Analytics workspace is configured for Azure Monitor for VMs, two management packs are forwarded to all the Windows computers connected to that workspace. The management packs are named *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* and *Microsoft.IntelligencePacks.VMInsights* and are written to *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs*. 

The data source used by the *ApplicationDependencyMonitor* management pack is **%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. The data source used by the *VMInsights* management pack is *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## Diagnostic and usage data

Microsoft automatically collects usage and performance data through your use of the Azure Monitor service. Microsoft uses this data to improve the quality, security, and integrity of the service. 

To provide accurate and efficient troubleshooting capabilities, the Map feature includes data about the configuration of your software. The data provides information such as the operating system and version, IP address, DNS name, and workstation name. Microsoft doesn't collect names, addresses, or other contact information.

For more information about data collection and usage, see the [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## Next steps

To learn how to use the Performance monitoring feature, see [View Azure Monitor for VMs Performance](vminsights-performance.md). To view discovered application dependencies, see [View Azure Monitor for VMs Map](vminsights-maps.md).
