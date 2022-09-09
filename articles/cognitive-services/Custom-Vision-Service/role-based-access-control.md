---
title: "Role-based access control - Custom Vision"
titleSuffix: Azure Cognitive Services
description: This article will show you how to configure role-based access control for your Custom Vision projects.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
---

# Role-based access control

Custom Vision supports Azure role-based access control (Azure RBAC), an authorization system for managing individual access to Azure resources. Using RBAC, you assign different team members different levels of permissions for your Custom Vision projects. For more information on RBAC, see the [Azure RBAC documentation](https://docs.microsoft.com/azure/role-based-access-control/).

## Add role assignment to Custom Vision resource

Azure RBAC can be assigned to a Custom Vision resource. To grant access to an Azure resource, you add a role assignment.
1. In the [Azure portal](https://ms.portal.azure.com/), select **All services**. 
1. Then select the **Cognitive Services**, and navigate to your specific Custom Vision training resource.
   > [!NOTE]
   > You can also set up RBAC for whole resource groups, subscriptions, or management groups. Do this by selecting the desired scope level and then navigating to the desired item (for example, selecting **Resource groups** and then clicking through to your wanted resource group).
1. Select **Access control (IAM)** on the left navigation pane.
1. Select the **Role assignments** tab to view the role assignments for this scope.
1. Select **Add** -> **Add role assignment**.
1. In the **Role** drop-down list, select a role you want to add.
1. In the **Select** list, select a user, group, service principal, or managed identity. If you don't see the security principal in the list, you can type the Select box to search the directory for display names, email addresses, and object identifiers.
1. Select **Save** to assign the role.

Within a few minutes, the target will be assigned the selected role at the selected scope.

## Custom Vision role types

Use the following table to determine access needs for your Custom Vision resources.

|Role  |Permissions  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Full access to the projects, including the ability to create, edit, or delete a project.        |
|`Cognitive Services Custom Vision Trainer`     | Full access except the ability to create or delete a project. Trainers can view and edit projects and train, publish, unpublish, or export the models.        |
|`Cognitive Services Custom Vision Labeler`     | Ability to upload, edit, or delete training images and create, add, remove, or delete tags. Labelers can view projects but can't update anything other than training images and tags.         |
|`Cognitive Services Custom Vision Deployment`     | Ability to publish, unpublish, or export the models. Deployers can view projects but can't update a project, training images, or tags.        |
|`Cognitive Services Custom Vision Reader`     | Ability to view projects. Readers can't make any changes.        |
