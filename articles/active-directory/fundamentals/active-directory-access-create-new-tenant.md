---
title: Quickstart - Access & create new tenant - Azure AD
description: Instructions about how to find Azure Active Directory and how to create a new tenant for your organization. 
services: active-directory
author: ajburnle
manager: daveba

ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: "it-pro, seodec18, fasttrack-edit"
ms.collection: M365-identity-device-management
---

# Quickstart: Create a new tenant in Azure Active Directory
You can do all of your administrative tasks using the Azure Active Directory (Azure AD) portal, including creating a new tenant for your organization. 

In this quickstart, you'll learn how to get to the Azure portal and Azure Active Directory, and you'll learn how to create a basic tenant for your organization.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/) before you begin.

## Create a new tenant for your organization
After you sign in to the Azure portal, you can create a new tenant for your organization. Your new tenant represents your organization and helps you to manage a specific instance of Microsoft cloud services for your internal and external users.

### To create a new tenant

1. Sign in to your organization's [Azure portal](https://portal.azure.com/).

1. From the Azure portal menu, select **Create a resource**.  

    ![Azure Active Directory Create resoure page](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Search for and select **Azure Active Directory**.

    The **Create directory** page appears.

    ![Azure Active Directory Create page](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  On the **Create directory** page, enter the following information:
    
    - Type _Contoso_ into the **Organization name** box.

    - Type _Contoso_ into the **Initial domain name** box.

    - Leave the _United States_ option in the **Country or region** box.

1. Select **Create**.

Your new tenant is created with the domain contoso.onmicrosoft.com.

## Clean up resources
If you're not going to continue to use this application, you can delete the tenant using the following steps:

- Ensure that you are signed in to the directory that you want to delete through the **Directory + subscription** filter in the Azure Portal, and switching to the target directory if needed.
- Select **Azure Active Directory**, and then on the **Contoso - Overview** page, select **Delete directory**.

    The tenant and its associated information is deleted.

    ![Overview page, with highlighted Delete directory button](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## Next steps
- Change or add additional domain names, see [How to add a custom domain name to Azure Active Directory](add-custom-domain.md)

- Add users, see [Add or delete a new user](add-users-azure-active-directory.md)

- Add groups and members, see [Create a basic group and add members](active-directory-groups-create-azure-portal.md)

- Learn about [role-based access using Privileged Identity Management](../../role-based-access-control/best-practices.md) and [Conditional Access](../../role-based-access-control/conditional-access-azure-management.md) to help manage your organization's application and resource access.

- Learn about Azure AD, including [basic licensing information, terminology, and associated features](active-directory-whatis.md).
