---
title: Delegate access governance to catalog creators in Azure AD entitlement management (Preview) - Azure Active Directory
description: Learn how to delegate access governance from IT administrators to catalog creators and project managers so that they can manage access themselves.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management


#Customer intent: As an administrator, I want to delegate access governance from IT administrators to department managers and project managers so that they can manage access themselves.

---

# Delegate access governance to catalog creators in Azure AD entitlement management (Preview)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) entitlement management is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

To delegate to users who aren't administrators, so that they can create their own catalogs, you can add those users to the Azure AD entitlement management-defined catalog creator role. You can add individual users, or you can add a group, whose members are then able to create catalogs.

## As an IT administrator, delegate to a catalog creator

Follow these steps to assign a user to the catalog creator role.

**Prerequisite role:** Global administrator or User administrator

1. In the Azure portal, click **Azure Active Directory** and then click **Identity Governance**.

1. In the left menu, in the **Entitlement management** section, click **Settings**.

1. Click **Edit**.

    ![Settings to add catalog creators](./media/entitlement-management-delegate/settings-delegate.png)

1. In the **Delegate entitlement management** section, click **Add catalog creators** to select the users or groups that you want to delegate this entitlement management role to.

1. Click **Select**.

1. Click **Save**.

## Next steps

- [Create and manage a catalog of resources](entitlement-management-catalog-create.md)
- [Delegate access governance to access package managers](entitlement-management-delegate-managers.md)

