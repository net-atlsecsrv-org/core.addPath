---
title: Migrate to Azure resource for authoring
titleSuffix: Azure Cognitive Services
description: Migrate to an Azure authoring resource key.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: diberry
---

# Migrate to an Azure resource authoring key

Language Understanding (LUIS) authoring authentication changed from an email account to an Azure resource. While not currently required, switching to an Azure resource will be enforced in the future.

## Why migrate?

Using an Azure resource for authoring allows you, as the owner of the resource, to control access to authoring. You can create and name authoring resources to manage different groups of authors.

For example, you are the owner of 2 LUIS apps, and you have different members who are collaborators on each app. You can create two different authoring resources and assign each app to each separate resource. Then assign each member as a contributor to the proper authoring resource depending on which app they collaborate on. The Azure authoring resource controls the authorization.

> [!Note]
> Before migration, co-authors are known as _collaborators_ on the LUIS app level. After migration, the Azure role of _contributor_ is used for the same functionality but on the Azure resource level.

## What is migrating?

Migration includes:

* All users of LUIS, owners and contributors.
* **All** apps.
* A **one-way** migration.

The owner can't choose a subset of apps to migrate and the process isn't reversible.

The migration is not:

* A process that collects collaborators and automatically moves or adds to the Azure authoring resource. You, as the app owner, need to complete this step. This step requires permissions to the appropriate resource.
* A process to create and assign a prediction runtime resource. If you need a prediction runtime resource, that is [a separate process](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) and is unchanged.

## How are the apps migrating?

The [LUIS portal](https://www.luis.ai) provides the migration process.

You will be asked to migrate if:

* You have apps on the email authentication system for authoring.
* And you are the app owner.

You can delay the migration process by canceling out of the window. You are periodically asked to migrate until you migrate or the migration deadline is passed. You can start the migration process from the top navigation bar's lock icon.

## Migration for the app owner

### Before you migrate

* **Required**, you need to have an [Azure subscription](https://azure.microsoft.com/free/). A part of the subscription process does require billing information. However, you can use the Free (`F0`) pricing tier when you use LUIS.
* **Optionally**, backup the apps from the LUIS portal's apps list by exporting each app or use the export [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Optionally**, save each app's collaborator's list. This email list is provided as part of the migration process.


**Authoring your LUIS app is free**, indicated by the `F0` tier. Learn [more about pricing tiers](luis-boundaries.md#key-limits).

If you do not have an Azure subscription, [sign up](https://azure.microsoft.com/free/).

### Migration steps

Follow [these migration steps](luis-migration-authoring-steps.md).

### After you migrate

After the migration process, all your LUIS apps are now assigned to a single LUIS authoring resource.

You can create more authoring resources and assign from the **Manage -> Azure resources** page in the _LUIS portal_.

You can add contributors to the authoring resource from the _Azure portal_, on the **Access Control (IAM)** page for that resource. See [add contributor access](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) for more information.

|Portal|Purpose|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Create prediction and authoring resources.<br>* Assign contributors.|
|[LUIS](https://www.luis.ai)|* Migrate to new authoring resources.<br>* Assign or unassign prediction and authoring resources to apps from **Manage -> Azure resources** page.|

## Migration for the app contributor

Every user of LUIS needs to migrate, including collaborators/contributors. A collaborator must migrate to have access to the app.

> [!Note]
> If the owner of the LUIS app migrated and added the collaborator as a contributor on the Azure resource, the collaborator will still have no access to the app unless they also migrate.

### Before the app is migrated

You may choose to export an app you are a collaborator on, then import the app back into LUIS. The import process creates a new app with a new app ID, for which you are the owner.

### After the app is migrated

The app owner needs to [add your email to the Azure authoring resource as a collaborator](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

After the migration process, any apps you own are available on the **My apps** page of the LUIS portal.

## Troubleshooting

* LUIS authoring keys are only visible in the LUIS portal after the migration process is complete. If you create the authoring keys, such as with the LUIS CLI, the user still needs to complete the migration process.
* If a migrated user adds a non-migrated user as a contributor on their azure resource, the non-migrated user will have no access to the apps unless they migrate.
* If a non-migrated user is not an owner to any apps but he is a collaborator to other apps that are owned by others and the owners have undergone the migration process, this user will need to migrate to have access to the apps.
* If a non-migrated user added another migrated user as a collaborator to their app, an error will occur as you will not be able to add a migrated user as a collaborator to an app. The non-migrated user will then have to go through the migration process and create an azure resource and add the migrated user as a contributor to that resource.

## Next steps

* [How to migrate your app to an authoring resource](luis-migration-authoring-steps.md)
