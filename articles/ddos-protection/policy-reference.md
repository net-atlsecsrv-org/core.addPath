---
title: Built-in policy definitions for Azure DDoS Protection Standard
description: Lists Azure Policy built-in policy definitions for Azure DDoS Protection Standard. These built-in policy definitions provide common approaches to managing your Azure resources.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
---

# Azure Policy built-in definitions for Azure DDoS Protection Standard

This page is an index of [Azure Policy](../governance/policy/overview.md) built-in policy
definitions for Azure DDoS Protection Standard. For additional Azure Policy built-ins for other services, see
[Azure Policy built-in definitions](../governance/policy/samples/built-in-policies.md).

The name of each built-in policy definition links to the policy definition in the Azure portal. Use
the link in the **Version** column to view the source on the
[Azure Policy GitHub repo](https://github.com/Azure/azure-policy).

## Azure DDoS Protection Standard

|Name<br /><sub>(Azure portal)</sub> |Description |Effect(s) |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual networks should be protected by Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Protect your virtual networks against volumetric and protocol attacks with Azure DDoS Protection Standard. For more information, visit [https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md).|Modify, Audit, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Public IP addresses should have resource logs enabled for Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Enable resource logs for public IP addresses in diagnostic settings to stream to a Log Analytics workspace. Get detailed visibility into attack traffic and actions taken to mitigate DDoS attacks via notifications, reports and flow logs.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## Next steps

- See the built-ins on the [Azure Policy GitHub repo](https://github.com/Azure/azure-policy).
- Review the [Azure Policy definition structure](../governance/policy/concepts/definition-structure.md).
- Review [Understanding policy effects](../governance/policy/concepts/effects.md).
