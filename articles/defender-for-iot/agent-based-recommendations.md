---
title: Agent based recommendations
titleSuffix: Azure Defender for IoT
description: Learn about the concept of security recommendations and how they are used for Defender for IoT devices.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''

ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
---

# Security recommendations for IoT devices

Defender for IoT scans your Azure resources and IoT devices and provides security recommendations to reduce your attack surface.
Security recommendations are actionable and aim to aid customers in complying with security best practices.

In this article, you will find a list of recommendations, which can be triggered on your IoT devices.

## Agent based recommendations

Device recommendations provide insights and suggestions to improve device security posture.

| Severity | Name | Data Source | Description |
|--|--|--|--|
| Medium | Open Ports on device | Classic security module | A listening endpoint was found on the device. |
| Medium | Permissive firewall policy found in one of the chains. | Classic security module | Allowed firewall policy found (INPUT/OUTPUT). Firewall policy should deny all traffic by default, and define rules to allow necessary communication to/from the device. |
| Medium | Permissive firewall rule in the input chain was found | Classic security module | A rule in the firewall has been found that contains a permissive pattern for a wide range of IP addresses or ports. |
| Medium | Permissive firewall rule in the output chain was found | Classic security module | A rule in the firewall has been found that contains a permissive pattern for a wide range of IP addresses or ports. |
| Medium | Operation system baseline validation has failed | Classic security module | Device doesn't comply with [CIS Linux benchmarks](https://www.cisecurity.org/cis-benchmarks/). |

### Agent based operational recommendations

Operational recommendations provide insights and suggestions to improve security agent configuration.

| Severity | Name | Data Source | Description |
|--|--|--|--|
| Low | Agent sends unutilized messages | Classic security module | 10% or more of security messages were smaller than 4 KB during the last 24 hours. |
| Low | Security twin configuration not optimal | Classic security module | Security twin configuration is not optimal. |
| Low | Security twin configuration conflict | Classic security module | Conflicts were identified in the security twin configuration. |  |

## Next steps

- Defender for IoT service [Overview](overview.md)
- Learn how to [Access your security data](how-to-security-data-access.md)
- Learn more about [Investigating a device](how-to-investigate-device.md)
