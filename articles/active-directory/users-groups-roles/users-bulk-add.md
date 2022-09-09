---
title: Bulk create users (preview) in the Azure Active Directory portal | Microsoft Docs
description: Add users in bulk in the Azure AD admin center in Azure Active Directory
services: active-directory 
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
---

# Bulk create users (preview) in Azure Active Directory

Azure Active Directory (Azure AD) supports bulk user create and delete operations, bulk invite for guests, and supports downloading lists of users, groups, and group members.

## Bulk import service limits

Each bulk activity to create users can run for up to one hour. This enables bulk creation of at least 50,000 users.

## Required permissions

In order to bulk create users in the administration portal, you must be signed in as a Global administrator or User administrator.

## To bulk import users

1. [Sign in to your Azure AD organization](https://aad.portal.azure.com) with an account that is a User administrator in the organization.
1. In Azure AD, select **Users** > **Bulk create**.
1. On the **Bulk create user** page, select **Download** to receive a valid comma-separated values (CSV) file of user properties, and then add your new users.

   ![The CSV file contains names and IDs of the users to create](./media/users-bulk-add/add-csv-file.png)

1. When you finish editing the CSV file or if you have one of your own ready to upload, select the file under **Upload your CSV file** to be validated.

   ![Select a local CSV file in which you list the users you want to add](./media/users-bulk-add/upload-button.png)

1. When the file contents are validated, you must fix any errors before you can start the upload job.
1. When your file passes validation, select **Submit** to start the Azure batch job that adds the new user information. If there are errors, you can download and view the results file on the Bulk operation results page. The file contains the reason for each error.

## Check status

You can see the status of all of your pending bulk requests in the **Bulk operation results (preview)** page.

   ![Check upload status in the Bulk Operations Results page](./media/users-bulk-add/bulk-center.png)

Next, you can check to see that the users you created exist in the Azure AD organization either in the Azure portal or by using PowerShell.

## Verify users in the Azure portal

1. [Sign in to the Azure AD admin center](https://aad.portal.azure.com) with an account that is a User administrator in the organization.
1. In the navigation pane, select **Azure Active Directory**.
1. Under **Manage**, select **Users**.
1. Under **Show**, select **All users** and verify that the users you created are listed.

### Verify users with PowerShell

Run the following command:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

You should see that the users that you created are listed.

## Next steps

- [Bulk delete users](users-bulk-delete.md)
- [Download list of users](users-bulk-download.md)
- [Bulk restore users](users-bulk-restore.md)
