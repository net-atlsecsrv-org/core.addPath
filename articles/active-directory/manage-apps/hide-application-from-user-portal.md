---
title: Hide an Enterprise application from user's experience in Azure AD
description: How to hide an Enterprise application from user's experience in Azure Active Directory access panels or Microsoft 365 launchers.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
---

# Hide Enterprise applications from end-users in Azure Active Directory

Instructions for how to hide applications from end-users' MyApps panel or Microsoft 365 launcher. When an application is hidden, users still have permissions to the application. 

## Prerequisites

Application administrator privileges are required to hide an application from the MyApps panel and Microsoft 365 launcher.

Global administrator privileges are required to hide all Microsoft 365 applications.


## Hide an application from the end user
Use the following steps to hide an application from MyApps panel and Microsoft 365 application launcher.

1.	Sign in to the [Azure portal](https://portal.azure.com) as the global administrator for your directory.
2.	Select **Azure Active Directory**.
3.	Select **Enterprise applications**. The **Enterprise applications - All applications** blade opens.
4.	Under **Application Type**, select **Enterprise Applications**, if it isn't already selected.
5.	Search for the application you want to hide, and click the application.  The application's overview opens.
6.	Click **Properties**. 
7.	For the **Visible to users?** question, click **No**.
8.	Click **Save**.

> [!NOTE]
> These instructions apply only to Enterprise applications.

## Use Azure AD PowerShell to hide an application

To hide an application from the MyApps panel, you can manually add the HideApp tag to the service principal for the application. Run the following [AzureAD PowerShell](/powershell/module/azuread/#service_principals) commands to set the application's **Visible to Users?** property to **No**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## Hide Microsoft 365 applications from the MyApps panel

Use the following steps to hide all Microsoft 365 applications from the MyApps panel. The applications are still visible in the Office 365 portal.

1.	Sign in to the [Azure portal](https://portal.azure.com) as a global administrator for your directory.
2.	Select **Azure Active Directory**.
3.	Select **Users**.
4.  Select **User settings**.
5.	Under **Enterprise applications**, click **Manage how end users launch and view their applications.**
6.	For **Users can only see Office 365 apps in the Office 365 portal**, click **Yes**.
7.	Click **Save**.

## Next steps
* [See all my groups](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assign a user or group to an enterprise app](assign-user-or-group-access-portal.md)
* [Remove a user or group assignment from an enterprise app](./assign-user-or-group-access-portal.md)
* [Change the name or logo of an enterprise app](./add-application-portal-configure.md)
