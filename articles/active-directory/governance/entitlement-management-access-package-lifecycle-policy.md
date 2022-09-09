---
title: Change lifecycle settings for an access package in Azure AD entitlement management - Azure Active Directory
description: Learn how to change requestor information & lifecycle settings for an access package in Azure Active Directory entitlement management.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: 
ms.collection: M365-identity-device-management


#Customer intent: As an administrator, I want detailed information about how I can edit an access package to include requestor infromation to screen requestors and get requestors the resources they need to perform their job.

---
# Change lifecycle settings for an access package in Azure AD entitlement management

As an access package manager, you can change the lifecycle settings for an access package at any time by editing an existing policy. If you change the expiration date for a policy, the expiration date for requests that are already in a pending approval or approved state will not change.

This article describes how to change the lifecycle settings for an existing access package.

## Open requestor information
To ensure users have the right access to an access package, custom questions can be configured to ask users requesting access to certain access packages. Configuration options include: localization, required/optional, and text/multiple choice answer formats. Requestors  will see the questions when they request the package and approvers see the answers to the questions to help them make their decision. Use the following steps to configure questions in an access package:

## Open lifecycle settings

To change the lifecycle settings for an access package, you need to open the corresponding policy. Follow these steps to open the lifecycle settings for an access package.

**Prerequisite role:** Global administrator, User administrator, Catalog owner, or Access package manager

1. In the Azure portal, click **Azure Active Directory** and then click **Identity Governance**.

1. In the left menu, click **Access packages** and then open the access package.

1. Click **Policies** and then click the policy that has the lifecycle settings you want to edit.

    The Policy details pane opens at the bottom of the page.

    ![Access package - Policy details pane](./media/entitlement-management-shared/policy-details.png)

1. Click **Edit** to edit the policy.

    ![Access package - Edit policy](./media/entitlement-management-shared/policy-edit.png)

1. Click the **Lifecycle** tab to open the lifecycle settings.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## Next steps

- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md)