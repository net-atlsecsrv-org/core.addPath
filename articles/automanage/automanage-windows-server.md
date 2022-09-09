---
title: Azure Automanage for Windows Server
description: Learn about the Azure Automanage for virtual machines best practices for services that are automatically onboarded and configured for Windows Server machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
---


# Azure Automanage for virtual machines best practices - Windows Server

These Azure services are automatically onboarded for you when you use Automanage for virtual machines (VMs) on a Windows Server VM. They are essential to our best practices white paper, which you can find in our [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

For all of these services, we will auto-onboard, auto-configure, monitor for drift, and remediate if drift is detected. To learn more about this process, see [Azure Automanage for virtual machines](automanage-virtual-machines.md).

## Supported Windows Server versions

Automanage supports the following Windows Server versions:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## Participating services

|Service    |Description    |Environments Supported<sup>1</sup>    |Preferences supported<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM Insights Monitoring    |Azure Monitor for VMs monitors the performance and health of your virtual machines, including their running processes and dependencies on other resources. Learn [more](../azure-monitor/vm/vminsights-overview.md).    |Production    |No    |
|Backup    |Azure Backup provides independent and isolated backups to guard against unintended destruction of the data on your VMs. Learn [more](../backup/backup-azure-vms-introduction.md). Charges are based on the number and size of VMs being protected. Learn [more](https://azure.microsoft.com/pricing/details/backup/).    |Production    |Yes    |
|Azure Security Center    |Azure Security Center is a unified infrastructure security management system that strengthens the security posture of your data centers, and provides advanced threat protection across your hybrid workloads in the cloud. Learn [more](../security-center/security-center-introduction.md).  Automanage will configure the subscription where your VM resides to the free-tier offering of Azure Security Center. If your subscription is already onboarded to Azure Security Center, then Automanage will not reconfigure it.    |Production, Dev/Test    |No    |
|Microsoft Antimalware    |Microsoft Antimalware for Azure is a free real-time protection that helps identify and remove viruses, spyware, and other malicious software. It generates alerts when known malicious or unwanted software tries to install itself or run on your Azure systems. Learn [more](../security/fundamentals/antimalware.md). |Production, Dev/Test    |Yes    |
|Update Management    |You can use Update Management in Azure Automation to manage operating system updates for your virtual machines. You can quickly assess the status of available updates on all agent machines and manage the process of installing required updates for servers. Learn [more](../automation/update-management/overview.md).    |Production, Dev/Test    |No    |
|Change Tracking & Inventory    |Change Tracking and Inventory combines change tracking and inventory functions to allow you to track virtual machine and server infrastructure changes. The service supports change tracking across services, daemons software, registry, and files in your environment to help you diagnose unwanted changes and raise alerts. Inventory support allows you to query in-guest resources for visibility into installed applications and other configuration items.  Learn [more](../automation/change-tracking/overview.md).    |Production, Dev/Test    |No    |
|Azure Guest Configuration    | Guest Configuration policy is used to monitor the configuration and report on the compliance of the machine. The Automanage service will install the [Windows security baselines](/windows/security/threat-protection/windows-security-baselines) using the Guest Configuration extension. For Windows machines, the guest configuration service will automatically reapply the baseline settings if they are out of compliance. Learn [more](../governance/policy/concepts/guest-configuration.md).    |Production, Dev/Test    |No    |
|Azure Automation Account    |Azure Automation supports management throughout the lifecycle of your infrastructure and applications. Learn [more](../automation/automation-intro.md).    |Production, Dev/Test    |No    |
|Log Analytics Workspace    |Azure Monitor stores log data in a Log Analytics workspace, which is an Azure resource and a container where data is collected, aggregated, and serves as an administrative boundary. Learn [more](../azure-monitor/logs/design-logs-deployment.md).    |Production, Dev/Test    |No    |


<sup>1</sup> The environment selection is available when you are enabling Automanage. Learn [more](automanage-virtual-machines.md#environment-configuration). You can also adjust the default settings of the environment and set your own preferences within the best practices constraints.


## Next steps

Try enabling Automanage for virtual machines in the Azure portal.

> [!div class="nextstepaction"]
> [Enable Automanage for virtual machines in the Azure portal](quick-create-virtual-machines-portal.md)