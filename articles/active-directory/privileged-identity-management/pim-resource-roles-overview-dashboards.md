---
title: Resource dashboards for access reviews in PIM - Azure AD | Microsoft Docs
description: Describes how to use a resource dashboard to perform an access review in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
---

# Use a resource dashboard to perform an access review in Privileged Identity Management

You can use a resource dashboard to perform an access review in Privileged Identity Management (PIM). The Admin View dashboard in Azure Active Directory (Azure AD) has three primary components:

- A graphical representation of resource role activations
- Charts that display the distribution of role assignments by assignment type
- A data area containing information to new role assignments

![Screenshot of the Admin View dashboard, showing graphs and charts](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot of the Admin View dashboard, showing data lists](media/pim-resource-roles-overview-dashboards/role-settings.png)

The graphical representation of resource role activations covers the past seven days. This data is scoped to the selected resource, and displays activations for the most common roles (owner, contributor, user access administrator), and for all roles combined.

On one side of the activations graph, two charts display the distribution of role assignments by assignment type, for both users and groups. You can change the value to a percentage (or vice versa), by selecting a slice of the chart.

Below the charts are listed the number of users and groups with new role assignments over the last 30 days, and roles sorted by total assignments in descending order.

## Next steps

- [Start an access review for Azure resource roles in Privileged Identity Management](pim-resource-roles-start-access-review.md)
