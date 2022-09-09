---
title: Tag images in a labeling project
title.suffix: Azure Machine Learning
description: Learn how to use the data tagging tools in an Azure Machine Learning labeling project.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019

---

# Tag images in a labeling project

After your project administrator [creates a labeling project](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-labeling-projects#create-a-labeling-project) in Azure Machine Learning, you can use the labeling tool to rapidly prepare data for a Machine Learning project. This article describes:

> [!div class="checklist"]
> * How to access your labeling projects
> * The labeling tools
> * How to use the tools for specific labeling tasks

## Prerequisites

* The labeling portal URL for a running data labeling project
* A [Microsoft account](https://account.microsoft.com/account) or an Azure Active Directory account for the organization and project

> [!NOTE]
> The project administrator can find the labeling portal URL on the **Details** tab of the **Project details** page.

## Sign in to the project's labeling portal

Go to the labeling portal URL that's provided by the project administrator. Sign in by using the email account that the administrator used to add you to the team. For most users, it will be your Microsoft account. If the labeling project uses Azure Active Directory, that's how you'll sign in.

## Understand the labeling task

After you sign in, you'll see the project's overview page.

Go to **View detailed instructions**. These instructions are specific to your project. They explain the type of data that you're facing, how you should make your decisions, and other relevant information. After you read this information, return to the project page and select **Start labeling**.

## Common features of the labeling task

In all image-labeling tasks, you choose an appropriate tag or tags from a set that's specified by the project administrator. You can select the first nine tags by using the number keys on your keyboard.  

In image-classification tasks, you can choose to view multiple images simultaneously. Use the icons above the image area to select the layout. To select all the displayed images simultaneously, use **Select all**. To select individual images, use the circular selection button in the upper-right corner of the image. You must select at least one image to apply a tag. If you select multiple images, any tag that you select will be applied to all the selected images.

Here we've chosen a two-by-two layout and are about to apply the tag "Mammal" to the images of the bear and orca. The image of the shark was already tagged as "Cartilaginous fish," and the iguana hasn't been tagged yet.

![Multiple image layouts and selection](./media/how-to-label-images/layouts.png)

> [!Important] 
> Only switch layouts when you have a fresh page of unlabeled data. Switching layouts clears the page's in-progress tagging work.

Azure enables the **Submit** button when you've tagged all the images on the page. Select **Submit** to save your work.

After you submit tags for the data at hand, Azure refreshes the page with a new set of images from the work queue.

## Tag images for multi-class classification

If your project is of type "Image Classification Multi-Class," you'll assign a single tag to the entire image. To review the directions at any time, go to the **Instructions** page and select **View detailed instructions**.

If you realize that you made a mistake after you assign a tag to an image, you can fix it. Select the "**X**" on the label that's displayed below the image to clear the tag. Or, select the image and choose another class. The newly selected value will replace the previously applied tag.

## Tag images for multi-label classification

If you're working on a project of type "Image Classification Multi-Label," you'll apply one *or more* tags to an image. To see the project-specific directions, select **Instructions** and go to **View detailed instructions**.

Select the image that you want to label and then select the tag. The tag is applied to all the selected images, and then the images are deselected. To apply more tags, you must reselect the images. The following animation shows multi-label tagging:

1. **Select all** is used to apply the "Ocean" tag.
1. A single image is selected and tagged "Closeup."
1. Three images are selected and tagged "Wide angle."

![Animation shows multilabel flow](./media/how-to-label-images/multilabel.gif)

To correct a mistake, click the "**X**" to clear an individual tag or select the images and then select the tag, which clears the tag from all the selected images. This scenario is shown here. Clicking on "Land" will clear that tag from the two selected images.

![A screenshot shows multiple deselections](./media/how-to-label-images/multiple-deselection.png)

Azure will only enable the **Submit** button after you've applied at least one tag to each image. Select **Submit** to save your work.

## Tag images and specify bounding boxes for object detection

If your project is of type "Object Identification (Bounding Boxes)," you'll specify one or more bounding boxes in the image and apply a tag to each box. Images can have multiple bounding boxes, each with a single tag. Use **View detailed instructions** to determine if multiple bounding boxes are used in your project.

1. Select a tag for the bounding box that you plan to create.
1. Select the **Rectangular box** tool ![Rectangular box tool](./media/how-to-label-images/rectangular-box-tool.png) or select "R."
3. Click and drag diagonally across your target to create a rough bounding box. To adjust the bounding box, drag the edges or corners.

![A screenshot shows basic bounding box creation.](./media/how-to-label-images/bounding-box-sequence.png)

To delete a bounding box, click the X-shaped target that appears next to the bounding box after creation.

You can't change the tag of an existing bounding box. If you make a tag-assignment mistake, you have to delete the bounding box and create a new one with the correct tag.

By default, you can edit existing bounding boxes. The **Lock/unlock regions** tool ![Lock/unlock regions tool](./media/how-to-label-images/lock-bounding-boxes-tool.png) or "L" toggles that behavior. If regions are locked, you can only change the shape or location of a new bounding box.

Use the **Regions manipulation** tool ![Regions manipulation tool](./media/how-to-label-images/regions-tool.png) or "M" to adjust an existing bounding box. Drag the edges or corners to adjust the shape. Click in the interior to be able to drag the whole bounding box. If you can't edit a region, you've probably toggled the **Lock/unlock regions** tool.

Use the **Template-based box** tool ![Template-box tool](./media/how-to-label-images/template-box-tool.png) or "T" to create multiple bounding boxes of the same size. If the image has no bounding boxes and you activate template-based boxes, the tool will produce 50-by-50-pixel boxes. If you create a bounding box and then activate template-based boxes, any new bounding boxes will be the size of the last box that you created. Template-based boxes can be resized after placement. Resizing a template-based box only resizes that particular box.

To delete *all* bounding boxes in the current image, select the **Delete all regions** tool ![Delete regions tool](./media/how-to-label-images/delete-regions-tool.png).

After you create the bounding boxes for an image, select **Submit** to save your work, or your work in progress won't be saved.

## Finish up

When you submit a page of tagged data, Azure assigns new unlabeled data to you from a work queue. If there's no more unlabeled data available, you'll get a message noting this along with a link to the portal home page.

When you're done labeling, select your name in the upper-right corner of the labeling portal and then select **sign-out**. If you don't sign out, eventually Azure will "time you out" and assign your data to another labeler.

## Next steps

* Learn to [train image classification models in Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Read about [object detection using Azure and the "Faster R-CNN" technique](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
