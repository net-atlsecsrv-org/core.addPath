---
title: Azure Security Control - Incident Response
description: Azure Security Control Incident Response
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark

---

# Security Control: Incident Response

Protect the organization's information, as well as its reputation, by developing and implementing an incident response infrastructure (e.g., plans, defined roles, training, communications, management oversight) for quickly discovering an attack and then effectively containing the damage, eradicating the attacker's presence, and restoring the integrity of the network and systems.

## 10.1: Create an incident response guide

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

Build out an incident response guide for your organization. Ensure that there are written incident response plans that define all roles of personnel as well as phases of incident handling/management from detection to post-incident review.  

- [Guidance on building your own security incident response process](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center's Anatomy of an Incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Leverage NIST's Computer Security Incident Handling Guide to aid in the creation of your own incident response plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## 10.2: Create an incident scoring and prioritization procedure

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.2 | 19.8 | Customer |

Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. The severity is based on how confident Security Center is in the finding or the analytic used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert. 

Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Use tags to organize your Azure resources](../../azure-resource-manager/management/tag-resources.md)

## 10.3: Test security response procedures

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.3 | 19 | Customer |

Conduct exercises to test your systems’ incident response capabilities on a regular cadence to help protect your Azure resources. Identify weak points and gaps and revise plan as needed.

- [NIST's publication - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## 10.4: Provide security incident contact details and configure alert notifications for security incidents

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.4 | 19.5 | Customer |

Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that your data has been accessed by an unlawful or unauthorized party. Review incidents after the fact to ensure that issues are resolved.

- [How to set the Azure Security Center Security Contact](../../security-center/security-center-provide-security-contact-details.md)

## 10.5: Incorporate security alerts into your incident response system

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.5 | 19.6 | Customer |

Export your Azure Security Center alerts and recommendations using the Continuous Export feature to help identify risks to Azure resources. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

- [How to configure continuous export](../../security-center/continuous-export.md)

- [How to stream alerts into Azure Sentinel](../../sentinel/connect-azure-security-center.md)

## 10.6: Automate the response to security alerts

| Azure ID | CIS IDs | Responsibility |
|--|--|--|
| 10.6 | 19 | Customer |

Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations to protect your Azure resources.

- [How to configure Workflow Automation and Logic Apps](../../security-center/workflow-automation.md)


## Next steps

- See the next Security Control: [Penetration Tests and Red Team Exercises](security-control-penetration-tests-red-team-exercises.md)