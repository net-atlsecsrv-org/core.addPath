---
title: Azure Security Center Readiness Roadmap | Microsoft Docs
description: This document provides you a readiness roadmap to ramp up on Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin

ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin

---
# Azure Security Center readiness roadmap
This document provides you a readiness roadmap that will assist you to get started with Azure Security Center.

## Understanding Security Center
Azure Security Center provides unified security management and advanced threat protection for workloads running in Azure, on-premises, and in other clouds. 

Use the following resources to get started with Security Center.

Articles
- [Introduction to Azure Security Center](security-center-introduction.md)
- [Azure Security Center quickstart guide](security-center-get-started.md)

Videos
- [Quick Introduction Video](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Overview of Security Center Prevention, Detection and Response Capabilities](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## Planning and operations

To take full advantage of Security Center, it is important to understand how different individuals or teams in your organization use the service to meet secure operations, monitoring, governance, and incident response needs.

Use the following resources to assist you during the planning and operations processes.

- [Azure Security Center planning and operations guide](security-center-planning-and-operations-guide.md)


### Onboarding computers to Security Center
Security Center automatically detects any Azure subscriptions or workspaces not protected by Azure Defender. This includes Azure subscriptions using Security Center Free and workspaces that do not have the security solution enabled.

Use the following resources to assist you during the onboarding processes.

- [Onboard non-Azure computers](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid - Overview](https://youtu.be/NMa4L_M597k)

## Mitigating security issues using Security Center
Security Center automatically collects, analyzes, and integrates log data from your Azure resources, the network, and connected partner solutions, like firewall and endpoint protection solutions, to detect real threats and reduce false positives.

Use the following resources to assist you to manage security alerts and protect your resources.

Articles    
- [Security health monitoring in Azure Security Center](./security-center-monitoring.md)
- [Protecting your network in Azure Security Center](./security-center-network-recommendations.md)
- [Protecting Azure SQL service and data in Azure Security Center](./security-center-remediate-recommendations.md)


Video    
- [Mitigating Security Issues using Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### Security Center for incident response
To reduce costs and damage, it's important to have an incident response plan in place before an attack takes place. You can use Azure Security Center in different stages of an incident response.

Use the following resources to understand how Security Center can be incorporated in your incident response process.

Videos    
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g)

Articles    
* [Using Azure Security Center for an incident response](./tutorial-security-incident.md)
* [Automate response with Workflow Automation](workflow-automation.md)

## Advanced cloud defense

Azure VMs can take advantage of advanced cloud defense capabilities in Security Center. These capabilities include just-in-time virtual machine (VM) access, and adaptive application controls.

Use the following resources to learn how to use these capabilities in Security Center.

Videos    
* [Azure Security Center – Just-in-time VM Access](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck)

Articles    
* [Manage virtual machine access using just-in-time](./security-center-just-in-time.md)
* [Adaptive Application Controls in Azure Security Center](./security-center-adaptive-application.md)

## Hands-on activities

* [Security Center hands-on lab](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## Additional resources
* [Security Center Documentation Page](./index.yml)
* [Security Center REST API Documentation Page](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure Security Center frequently asked questions (FAQ)](./faq-general.md)
* [Security Center Pricing Page](https://azure.microsoft.com/pricing/details/security-center/)
* [Identity security best practices](../security/fundamentals/identity-management-best-practices.md)
* [Network security best practices](../security/fundamentals/network-best-practices.md)
* [PaaS recommendations](../security/fundamentals/paas-deployments.md)
* [Compliance](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log analytics customers can now use Azure Security Center to protect their hybrid cloud workloads](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## Community Resources

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Q&A page for Security Center](/answers/topics/azure-security-center.html)