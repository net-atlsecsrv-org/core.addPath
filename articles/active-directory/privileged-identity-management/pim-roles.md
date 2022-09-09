---
title: Roles you cannot manage in Privileged Identity Management - Azure Active Directory | Microsoft Docs
description: Describes the roles you cannot manage in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''

ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
---

# Roles you can't manage in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) enables you to manage all [Azure AD roles](../users-groups-roles/directory-assign-admin-roles.md) and all [Azure resource roles](../../role-based-access-control/built-in-roles.md). These roles also include your custom roles attached to your management groups, subscriptions, resource groups, and resources. However, there are few roles that you cannot manage. This article describes the roles you cannot manage in Privileged Identity Management.

## Classic subscription administrator roles

You cannot manage the following classic subscription administrator roles in Privileged Identity Management:

- Account Administrator
- Service Administrator
- Co-Administrator

For more information about the classic subscription administrator roles, see [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## What about Office 365 admin roles?

Roles within Exchange Online or SharePoint Online, except for Exchange Administrator and SharePoint Administrator, are not represented in Azure AD and so cannot be managed in Privileged Identity Management. For more information about these Office 365 services, see [Office 365 admin roles](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint Administrator has administrative access to SharePoint Online through the SharePoint Online admin center, and can perform almost any task in SharePoint Online. Eligible users may experience delays using this role within SharePoint after activating in Privileged Identity Management.

## Next steps

- [Assign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
