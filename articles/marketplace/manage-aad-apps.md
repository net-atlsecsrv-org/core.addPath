---
title: Add and manage Azure AD applications - Azure Marketplace
description: Learn how to add and manage Azure AD applications for the commercial marketplace program in Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
---

# Add and manage Azure AD applications

**Appropriate roles**

- Owner
- Manager

You can allow applications or services that are part of your company's Azure Active Directory (Azure AD) to access your Partner Center account.

## Add existing Azure AD applications

To add applications that already exist in your company's Azure Active Directory:

1. From the **Users** page (under **Account settings**), select **Add Azure AD applications**.
1. Select one or more Azure AD applications from the list that appears. You can use the search box to search for specific Azure AD applications. If you select more than one Azure AD application to add to your Partner Center account, you must assign them the same role or set of custom permissions. To add multiple Azure AD applications with different roles/permissions, repeat these steps for each role or set of custom permissions.
1. When you are finished selecting Azure AD applications, select **Add selected**.
1. In the **Roles** section, specify the role(s) or customized permissions for the selected Azure AD application(s).
1. Select **Save**.

## Add new Azure AD applications

If you want to grant Partner Center access to a brand-new Azure AD application account, you can create one in the **Users** section. This will create a new account in your company work account (Azure AD tenant), not just in your Partner Center account. If you are primarily using this Azure AD application for Partner Center authentication, and don't need users to access it directly, you can enter any valid address for the **Reply URL** and **App ID URI**, as long as those values are not used by any other Azure AD application in your directory.

1. From the **Users** page (under **Account settings**), select **Add Azure AD applications**.
1. On the next page, select **New Azure AD application**.
1. Enter the **Reply URL** for the new Azure AD application. This is the URL where users can sign in and use your Azure AD application (sometimes also known as the App URL or Sign-On URL). The *Reply URL* can't be longer than 256 characters and must be unique within your directory.
1. Enter the **App ID URI** for the new Azure AD application. This is a logical identifier for the Azure AD application that is presented when a single sign-on request is sent to Azure AD. The *App ID URI* must be unique for each Azure AD application in your directory. This ID can't be longer than 256 characters. For more info about the App ID URI, see [Integrating applications with Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. In the **Roles** section, specify the role(s) or customized permissions for the Azure AD application.
1. Select **Save**.

After you add or create an Azure AD application, you can return to the **Users** section and select the application name to review settings for the application, including the Tenant ID, Client ID, Reply URL, and App ID URI.

## Remove an Azure AD application

To remove an application from your work account (Azure AD tenant), go to **Users** (under **Account settings**), select the application that you would like to remove using the checkbox in the far right column, then choose **Remove** from the available actions. A pop-up window will appear for you to confirm that you want to remove the selected application(s).

## Manage keys for an Azure AD application

If your Azure AD application reads and writes data in Microsoft Azure AD, it will need a key. You can create keys for an Azure AD application by editing its information in Partner Center. You can also remove keys that are no longer needed.

1. From the **Users** page (under **Account settings**), select the name of the Azure AD application. You'll see all of the active keys for the Azure AD application, including the date on which the key was created and when it will expire 50.
1. To remove a key that is no longer needed, select **Remove**.
1. To add a new key, select **Add new key**.
1. You will see a screen showing the **Client ID** and **Key values**. Be sure to print or copy this information, as you won't be able to access it again after you leave this page.
1. If you want to create more keys, select **Add another key**.
