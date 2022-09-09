---
title: Create an access review of Azure AD roles in PIM - Azure Active Directory | Microsoft Docs
description: Learn how to create an access review of Azure AD roles in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''

ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
---

# Create an access review of Azure AD roles in PIM

Access to privileged Azure AD roles for employees changes over time. To reduce the risk associated with stale role assignments, you should regularly review access. You can use Azure Active Directory (Azure AD) Privileged Identity Management (PIM) to create access reviews for privileged Azure AD roles. You can also configure recurring access reviews that occur automatically.

This article describes how to create one or more access reviews for privileged Azure AD roles.

## Prerequisites

- [Privileged Role Administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## Open access reviews

1. Sign in to [Azure portal](https://portal.azure.com/) with a user that is a member of the Privileged Role Administrator role.

1. Open **Azure AD Privileged Identity Management**.

1. In the left menu, click **Azure AD roles** and then click **Access reviews**.

1. Under Manage, click **Access reviews**.

    ![Azure AD roles - Access reviews](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## Start the access review

Once you have specified the settings for an access review, click **Start**. The access review will appear in your list with an indicator of its status.

![Access reviews list](./media/pim-how-to-start-security-review/access-reviews-list.png)

By default, Azure AD sends an email to reviewers shortly after the review starts. If you choose not to have Azure AD send the email, be sure to inform the reviewers that an access review is waiting for them to complete. You can show them the instructions for how to [review access to Azure AD roles](pim-how-to-perform-security-review.md).

## Manage the access review

You can track the progress as the reviewers complete their reviews on the **Overview** page of the access review. No access rights are changed in the directory until the [review is completed](pim-how-to-complete-review.md).

![Access reviews progress](./media/pim-how-to-start-security-review/access-review-overview.png)

If this is a one-time review, then after the access review period is over or the administrator stops the access review, follow the steps in [Complete an access review of Azure AD roles](pim-how-to-complete-review.md) to see and apply the results.  

To manage a series of access reviews, navigate to the access review, and you will find upcoming occurrences in Scheduled reviews, and edit the end date or add/remove reviewers accordingly.

Based on your selections in **Upon completion settings**, auto-apply will be executed after the review's end date or when you manually stop the review. The status of the review will change from **Completed** through intermediate states such as **Applying** and finally to state **Applied**. You should expect to see denied users, if any, being removed from roles in a few minutes.

## Next steps

- [Review access to Azure AD roles](pim-how-to-perform-security-review.md)
- [Complete an access review of Azure AD roles](pim-how-to-complete-review.md)
- [Create an access review of Azure resource roles](pim-resource-roles-start-access-review.md)
