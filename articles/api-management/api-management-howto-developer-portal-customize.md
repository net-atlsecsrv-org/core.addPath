---
title: Access and customize the managed developer portal - Azure API Management | Microsoft Docs
description: Learn how to use the managed version of the developer portal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
---

# Access and customize developer portal

Developer portal is an automatically generated, fully customizable website with the documentation of your APIs. It is where API consumers can discover your APIs, learn how to use them, and request access.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Access the managed version of the developer portal
> * Navigate its administrative interface
> * Customize the content
> * Publish the changes
> * View the published portal

You can find more details on the developer portal in the [Azure API Management developer portal overview](api-management-howto-developer-portal.md).

![API Management developer portal - admin mode](media/api-management-howto-developer-portal-customize/cover.png)

## Prerequisites

- Complete the following quickstart: [Create an Azure API Management instance](get-started-create-service-instance.md)
- Import and publish an Azure API Management instance. For more information, see [Import and publish](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## Access the portal as an administrator

Follow the steps below to access the managed version of the portal.

1. Go to your API Management service instance in the Azure portal.
1. Click on the **Developer portal** button in the top navigation bar. A new browser tab with an administrative version of the portal will open.

## Understand the portal's administrative interface

### Default content 

If you're accessing the portal for the first time, the default content will be automatically provisioned in the background. Default content has been designed to showcase portal's capabilities and minimize the amount of customizations needed to personalize your portal. You can learn more about what is included in the portal content in the [Azure API Management developer portal overview](api-management-howto-developer-portal.md).

### Visual editor

You can customize the content of the portal with the visual editor. The menu sections on the left let you create or modify pages, media, layouts, menus, styles, or website settings. The menu items on the bottom let you switch between viewports (for example, mobile or desktop), view the elements of the portal visible to authenticated or anonymous users, or save or undo actions.

You can add rows to a page by clicking on a blue icon with a plus sign. Widgets (for example, text, images, or APIs list) can be added by pressing a grey icon with a plus sign. You can rearrange items in a page with the drag-and-drop interaction. 

### Layouts and pages

![Pages and layouts](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Layouts define how pages are displayed. For example, in the default content, there are two layouts - one applies to the home page, and the other to all remaining pages.

A layout gets applied to a page by matching its URL template to the page's URL. For example, layout with a URL template of `/wiki/*` will be applied to every page with the `/wiki/` segment in the URL: `/wiki/getting-started`, `/wiki/styles`, etc.

In the image above, content belonging to the layout is marked in blue, while the page is marked in red. The menu sections are marked respectively.

### Styling guide

![Styling guide](media/api-management-howto-developer-portal-customize/styling-guide.png)

Styling guide is a panel created with designers in mind. It allows for overseeing and styling all the visual elements in your portal. The styling is hierarchical - many elements inherit properties from other elements. For example, button elements use colors for text and background. To change a button's color, you need to change the original color variant.

To edit a variant, click on it and select the pencil icon that appears on top of it. Once you make the changes in the pop-up window, close it.

### Save button

![Save button](media/api-management-howto-developer-portal-customize/save-button.png)

Whenever you make a change in the portal, you need to save it manually by pressing the **Save** button in the menu at the bottom. When you save your changes, the modified content is automatically uploaded to your API Management service.

## Customize the portal's content

Before you make your portal available to the visitors, you should personalize the automatically generated content. Recommended changes include the layouts, styles, and the content of the home page.

> [!NOTE]
> Due to integration considerations, the following pages can't be removed or moved under a different URL: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### Home page

The default **Home** page is filled with dummy content. You can either remove the whole sections with the content or keep the structure and adjust the elements one by one. Replace the generated text and images with your own and make sure the links point to desired locations.

### Layouts

Replace the automatically generated logo in the navigation bar with your own image.

### Styling

Although you don't need to adjust any styles, you may consider adjusting particular elements. For example, change the primary color to match your brand's color.

### Customization example

In the video below we demonstrate how to edit the content of the portal, customize the website's look, and publish the changes.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish"> </a>Publish the portal

To make your portal and its latest changes available to visitors, you need to publish it.

1. Make sure you saved your changes by clicking on the **Save** icon.
1. Click on **Publish website** in the **Operations** section of the menu. This operation may take a few minutes.  
    ![Publish portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> The portal needs to be republished after API Management service configuration changes, such as assigning a custom domain, updating the identity providers, setting delegation, specifying sign-in and product terms, and more.

## Visit the published portal

After you publish the portal, you can access it at the same URL as the administrative panel, for example `https://contoso-api.developer.azure-api.net`. View it in a separate browser session (incognito / private browsing mode) as an external visitor.

## Apply the CORS policy on APIs

You need to enable CORS (cross-origin resource sharing) on your APIs to let the visitors of your portal test the APIs through the built-in interactive console. Refer to [this documentation article](api-management-howto-developer-portal.md#cors) for more details.

## Next steps
- [Optimize and save on your cloud spending](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Learn more about the developer portal:

- [Azure API Management developer portal overview](api-management-howto-developer-portal.md)