---
title: Use a group to manage access to SaaS apps - Azure AD | Microsoft Docs
description: How to use groups in Azure Active Directory to assign access to SaaS applications that are integrated with Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba

ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
---
# Using a group to manage access to SaaS applications

Using Azure Active Directory (Azure AD) with an Azure AD Premium license plan, you can use groups to assign access to a SaaS application that's integrated with Azure AD. For example, if you want to assign access for the marketing department to use five different SaaS applications, you can create a group that contains the users in the marketing department, and then assign that group to these five SaaS applications that are needed by the marketing department. This way you can save time by managing the membership of the marketing department in one place. Users then are assigned to the application when they are added as members of the marketing group, and have their assignments removed from the application when they are removed from the marketing group. This capability can be used with hundreds of applications that you can add from within the Azure AD Application Gallery.

> [!IMPORTANT]
> You can use this feature only after you start an Azure AD Premium trial or purchase Azure AD Premium license plan.
> Group-based assignment is supported only for security groups.
> Nested group memberships are not supported for group-based assignment to applications at this time.

## To assign access for a user or group to a SaaS application

1. In the [Azure AD admin center](https://aad.portal.azure.com), select **Enterprise applications**.
2. Select an application that you added from the Application Gallery to open it.
3. Select **Users and groups**, and then select **Add user**.
4. On **Add Assignment**, select **Users and groups** to open the **Users and groups** selection list.
6. Select as many groups or users as you want, then click or tap **Select** to add them to the **Add Assignment** list. You can also assign a role to a user at this stage.
7. Select **Assign** to assign the users or groups to the selected enterprise application.

## Next steps
These articles provide additional information on Azure Active Directory.

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Azure Active Directory cmdlets for configuring group settings](../enterprise-users/groups-settings-cmdlets.md)
* [What is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
