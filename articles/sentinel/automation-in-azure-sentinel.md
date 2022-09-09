---
title: Introduction to automation in Azure Sentinel | Microsoft Docs
description: This article introduces the Security Orchestration, Automation, and Response (SOAR) capabilities of Azure Sentinel and describes its SOAR components - automation rules and playbooks.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin

ms.assetid:
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
---
# Security Orchestration, Automation, and Response (SOAR) in Azure Sentinel

This article describes the Security Orchestration, Automation, and Response (SOAR) capabilities of Azure Sentinel, and shows how the use of automation rules and playbooks in response to security threats increases your SOC's effectiveness and saves you time and resources.

## Azure Sentinel as a SOAR solution

### The problem

SIEM/SOC teams are typically inundated with security alerts and incidents on a regular basis, at volumes so large that available personnel are overwhelmed. This results all too often in situations where many alerts are ignored and many incidents aren't investigated, leaving the organization vulnerable to attacks that go unnoticed.

### The solution

Azure Sentinel, in addition to being a Security Information and Event Management (SIEM) system, is also a platform for Security Orchestration, Automation, and Response (SOAR). One of its primary purposes is to automate any recurring and predictable enrichment, response, and remediation tasks that are the responsibility of your Security Operations Center and personnel (SOC/SecOps), freeing up time and resources for more in-depth investigation of, and hunting for, advanced threats. Automation takes a few different forms in Azure Sentinel, from automation rules that centrally manage the automation of incident handling and response, to playbooks that run predetermined sequences of actions to provide powerful and flexible advanced automation to your threat response tasks.

## Automation rules

Automation rules are a new concept in Azure Sentinel. This feature allows users to centrally manage the automation of incident handling. Besides letting you assign playbooks to incidents (not just to alerts as before), automation rules also allow you to automate responses for multiple analytics rules at once, automatically tag, assign, or close incidents without the need for playbooks, and control the order of actions that are executed. Automation rules will streamline automation use in Azure Sentinel and will enable you to simplify complex workflows for your incident orchestration processes.

Learn more with this [complete explanation of automation rules](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - The **automation rules** feature is currently in **PREVIEW**. See the [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

## Playbooks

A playbook is a collection of response and remediation actions and logic that can be run from Azure Sentinel as a routine. A playbook can help automate and orchestrate your threat response, it can integrate with other systems both internal and external, and it can be set to run automatically in response to specific alerts or incidents, when triggered by an analytics rule or an automation rule, respectively. It can also be run manually on-demand, in response to alerts, from the incidents page.

Playbooks in Azure Sentinel are based on workflows built in [Azure Logic Apps](../logic-apps/logic-apps-overview.md), a cloud service that helps you schedule, automate, and orchestrate tasks and workflows across systems throughout the enterprise. This means that playbooks can take advantage of all the power and customizability of Logic Apps' integration and orchestration capabilities and easy-to-use design tools, and the scalability, reliability, and service level of a Tier 1 Azure service.

Learn more with this [complete explanation of playbooks](automate-responses-with-playbooks.md).

## Next steps

In this document, you learned how Azure Sentinel uses automation to help your SOC operate more effectively and efficiently.

- To learn about automation of incident handling, see [Automate incident handling in Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- To learn more about advanced automation options, see [Automate threat response with playbooks in Azure Sentinel](automate-responses-with-playbooks.md).
- For help in implementing automation rules and playbooks, see [Tutorial: Use playbooks to automate threat responses in Azure Sentinel](tutorial-respond-threats-playbook.md).
