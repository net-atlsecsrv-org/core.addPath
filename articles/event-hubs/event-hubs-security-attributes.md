---
title: Security attributes for Azure Event Hubs
description: A checklist of security attributes for evaluating Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess

ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin

---
# Security attributes for Azure Event Hubs

This article documents the security attributes built into Azure Event Hubs.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## Preventative

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Encryption at rest (such as server-side encryption, server-side encryption with customer-managed keys, and other encryption features)|  Yes | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Yes | |
| Encryption key handling (CMK, BYOK, etc.)| No |  |
| Column level encryption (Azure Data Services)| N/A | |
| API calls encrypted| Yes |  |

## Network segmentation

| Security Attribute | Yes/No | Notes |
|---|---|--|
| Service endpoint support| Yes |  |
| VNet injection support| No | |
| Network isolation and firewalling support| Yes |  |
| Forced tunneling support| No |  |

## Detection

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Yes | |

## Identity and access management

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Authentication| Yes | |
| Authorization|  Yes | |


## Audit trail

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Control and management plane logging and audit| Yes |  |
| Data plane logging and audit| Yes |   |

## Configuration management

| Security Attribute | Yes/No | Notes|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Yes | |
