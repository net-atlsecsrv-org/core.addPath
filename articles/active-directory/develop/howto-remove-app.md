---
title: "How to: Remove a registered app from the Microsoft identity platform | Azure"
titleSuffix: Microsoft identity platform
description: In this how-to, you learn how to remove an application registered with the Microsoft identity platform.
services: active-directory
author: rwike77
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
#Customer intent: As an application developer, I want to know how to remove my application from the Microsoft identity registered.
---

# How to remove an application registered with the Microsoft identity platform

Enterprise developers and software-as-a-service (SaaS) providers who have registered applications with the Microsoft identity platform may need to remove an application's registration.

In the following sections, you learn how to:

* Remove an application authored by you or your organization
* Remove an application authored by another organization

## Prerequisites

* An [application registered in your Azure AD tenant](quickstart-register-app.md)

## Remove an application authored by you or your organization

Applications that you or your organization have registered are represented by both an application object and service principal object in your tenant. For more information, see [Application Objects and Service Principal Objects](./app-objects-and-service-principals.md).

> [!NOTE]
> Deleting an application will also delete its service principal object in the application's home directory. For multi-tenant applications, service principal objects in other directories will not be deleted.

To delete an application, be listed as an owner of the application or have admin privileges.

1. Sign in to the <a href="https://portal.azure.com/" target="_blank">Azure portal</a>.
1. If you have access to multiple tenants, use the **Directory + subscription** filter :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in the top menu to select the tenant in which the app is registered.
1. Search and select the **Azure Active Directory**. 
1. Under **Manage**, select **App registrations**  and select the application that you want to configure. Once you've selected the app, you'll see the application's **Overview** page.
1. From the **Overview** page, select **Delete**.
1. Read the deletion consequences.  Check the box if one appears at the bottom of the pane.
1. Select **Delete** to confirm that you want to delete the app.

## Remove an application authored by another organization

If you are viewing **App registrations** in the context of a tenant, a subset of the applications that appear under the **All apps** tab are from another tenant and were registered into your tenant during the consent process. More specifically, they are represented by only a service principal object in your tenant, with no corresponding application object. For more information on the differences between application and service principal objects, see [Application and service principal objects in Azure AD](./app-objects-and-service-principals.md).

In order to remove an application’s access to your directory (after having granted consent), the company administrator must remove its service principal. The administrator must have Global Admininstrator access, and can remove the application through the Azure portal or use the [Azure AD PowerShell Cmdlets](/previous-versions/azure/jj151815(v=azure.100)) to remove access.

## Next steps

Learn more about [application and service principal objects](app-objects-and-service-principals.md) in the Microsoft identity platform.
