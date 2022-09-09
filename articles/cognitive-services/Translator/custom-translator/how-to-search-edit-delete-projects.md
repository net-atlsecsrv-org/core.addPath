---
title: How to search, edit, and delete project - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator provides various ways to manage your projects in efficient manner. You can create multiple projects, search based on your criteria, edit your projects. Deleting a project is also possible in Custom Translator.  
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
#Customer intent: As a Custom Translator user, I want to understand how to search, edit, delete projects, so that I can manage my projects effeciently.
---
# Search, edit, and delete projects

Custom Translator provides multiple ways to manage your projects in efficient manner. You can create many projects, search based on your criteria, and edit your projects. Deleting a project is also possible in Custom Translator.  

## Search and filter projects

The filter tool allows you to search projects by different filter conditions. It filters like project name, status, source and target language, and category of the project.

1. Click on the filter button.

    ![Search project](media/how-to/how-to-search-project.png)

2. You can filter by any (or all) of the following fields: project name, source language, target language, category, and project availability.

3. Click apply.

    ![Search project filter options](media/how-to/how-to-search-project-filters.png)

4. Clear the filter to view all your projects by tapping “Clear”.

## Edit a project

Custom Translator gives you the ability to edit the name and description of a project. Other project metadata like the category, source language, and target language are not available for edit. The steps below describe how to edit a project.

1. Click on the pencil icon that appears when you hover over a project.

    ![Edit project](media/how-to/how-to-edit-project.png)

2. In the dialog, you can modify the project name, the description of the project, the category description, and the project label if no model is deployed. You cannot modify the category or language pair once the project is created.

    ![Edit project dialog](media/how-to/how-to-edit-project-dialog.png)

3. Click on the "Save" button.

## Delete a project

You can delete a project when you no longer need it. Make the project does not have models in active state such as deployed, training submitted, data processing, deploying, etc., otherwise, the delete operation would fail. Below steps describe how to delete a project.

1. Hover on any project record and click on the trash icon.

   ![Delete project](media/how-to/how-to-delete-project.png)

2. Confirm deletion. Deleting a project will delete all models that were created within that project. Deleting project will not affect your  documents.

   ![Delete confirmation dialog](media/how-to/how-to-delete-project-confirm.png)

## Next steps

- [Upload documents](how-to-upload-document.md) to start building your custom translation model.
