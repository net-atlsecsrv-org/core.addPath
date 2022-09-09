---
title: Onboard Azure Arc enabled server to Azure Sentinel
description: Learn how to add your Azure Arc enabled servers to Azure Sentinel and proactively monitor their security status.
ms.date: 11/16/2020
ms.topic: conceptual
---

# Onboard Azure Arc enabled servers to Azure Sentinel

This article is intended to help you onboard your Azure Arc enabled server to [Azure Sentinel](../../sentinel/overview.md) and start collecting security-related events. Azure Sentinel provides a single solution for alert detection, threat visibility, proactive hunting, and threat response across the enterprise.

## Prerequisites

Before you start, make sure that you've met the following requirements:

- A [Log Analytics workspace](../../azure-monitor/platform/data-platform-logs.md). For more information about Log Analytics workspaces, see [Designing your Azure Monitor Logs deployment](../../azure-monitor/platform/design-logs-deployment.md).

- Azure Sentinel [enabled in your subscription](../../sentinel/quickstart-onboard.md).

- You're machine or server is connected to Azure Arc enabled servers.

## Onboard Azure Arc enabled servers to Azure Sentinel

Azure Sentinel comes with a number of connectors for Microsoft solutions, available out of the box and providing real-time integration. For physical and virtual machines, you can install the Log Analytics agent that collects the logs and forwards them to Azure Sentinel. Arc enabled servers supports deploying the Log Analytics agent using the following methods:

- Using the VM extensions framework.

    This feature in Azure Arc enabled servers allows you to deploy the Log Analytics agent VM extension to a non-Azure Windows and/or Linux server. VM extensions can be managed using the following methods on your hybrid machines or servers managed by Arc enabled servers:

    - The [Azure portal](manage-vm-extensions-portal.md)
    - The [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager templates](manage-vm-extensions-template.md)

- Using Azure Policy.

    Using this approach, you use the Azure Policy [Deploy Log Analytics agent to Linux or Windows Azure Arc machines](../../governance/policy/samples/built-in-policies.md#monitoring) built-in policy to audit if the Arc enabled server has the Log Analytics agent installed. If the agent is not installed, it automatically deploys it using a remediation task. Alternatively, if you plan to monitor the machines with Azure Monitor for VMs, instead use the [Enable Azure Monitor for VMs](../../governance/policy/samples/built-in-initiatives.md#monitoring) initiative to install and configure the Log Analytics agent.

We recommend installing the Log Analytics agent for Windows or Linux using Azure Policy.

After your Arc enabled servers are connected, your data starts streaming into Azure Sentinel and is ready for you to start working with. You can view the logs in the [built-in workbooks](../../sentinel/quickstart-get-visibility.md) and start building queries in Log Analytics to [investigate the data](../../sentinel/tutorial-investigate-cases.md).

## Next steps

Get started [detecting threats with Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).