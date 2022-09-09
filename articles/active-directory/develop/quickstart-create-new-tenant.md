---
title: Create an Azure Active Directory tenant
description: Learn how to create an Azure AD tenant to use for registering and building applications.
services: active-directory
author: rwike77
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
#Customer intent: As an application developer, I need to create an Microsoft identity environment so I can use it to register applications.
---

# Quickstart: Set up a tenant

The Microsoft identity platform allows developers to build apps targeting a wide variety of custom Microsoft 365 environments and identities. To get started using Microsoft identity platform, you will need access to an environment, also called an Azure AD tenant, that can register and manage apps, have access to Microsoft 365 data, and deploy custom Conditional Access and tenant restrictions.

A tenant is a representation of an organization. It's a dedicated instance of Azure AD that an organization or app developer receives when the organization or app developer creates a relationship with Microsoft-- like signing up for Azure, Microsoft Intune, or Microsoft 365.

Each Azure AD tenant is distinct and separate from other Azure AD tenants and has its own representation of work and school identities, consumer identities (if it's an Azure AD B2C tenant), and app registrations. An app registration inside of your tenant can allow authentications from accounts only within your tenant or all tenants.

## Determining environment type

There are two types of environments you can create. Deciding which you need is based solely on the types of users your app will authenticate.

* Work and school (Azure AD accounts) or Microsoft accounts (such as outlook.com and live.com)
* Social and local accounts (Azure AD B2C)

The quickstart is broken into two scenarios depending on the type of app you want to build.

## Work and school accounts, or personal Microsoft accounts

### Use an existing tenant

Many developers already have tenants through services or subscriptions that are tied to Azure AD tenants such as Microsoft 365 or Azure subscriptions.

1. To check the tenant, sign in to the [Azure portal](https://portal.azure.com) with the account you want to use to manage your application.
1. Check the upper right corner. If you have a tenant, you'll automatically be logged in and can see the tenant name directly under your account name.
   * Hover over your account name on the upper right-hand side of the Azure portal to see your name, email, directory / tenant ID (a GUID), and your domain.
   * If your account is associated with multiple tenants, you can select your account name to open a menu where you can switch between tenants. Each tenant has its own tenant ID.

> [!TIP]
> If you need to find the tenant ID, you can:
> * Hover over your account name to get the directory / tenant ID, or
> * Select **Azure Active Directory > Properties > Directory ID** in the Azure portal

If you don't have an existing tenant associated with your account, you'll see a GUID under your account name and you won't be able to perform actions like registering apps until you follow the steps of the next section.

### Create a new Azure AD tenant

If you don't already have an Azure AD tenant or want to create a new one for development, see the [quickstart](../fundamentals/active-directory-access-create-new-tenant.md) or simply follow the [directory creation experience](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). You will have to provide the following info to create your new tenant:

- **Organization name**
- **Initial domain** - this will be part of *.onmicrosoft.com. You can customize the domain more later.
- **Country or region**

> [!NOTE]
> When naming your tenant, use alphanumeric characters. Special characters are not allowed. The name must not exceed 256 characters.

## Social and local accounts

To begin building apps that sign in social and local accounts, you'll need to create an Azure AD B2C tenant. To begin, follow [creating an Azure AD B2C tenant](../../active-directory-b2c/tutorial-create-tenant.md).

## Next steps

* [Register an app](quickstart-register-app.md) and integrate with Microsoft identity platform. 
* Learn the [basics of authentication](authentication-scenarios.md).
* See [Associate or add an Azure subscription to your Azure Active Directory tenant](../fundamentals/active-directory-how-subscriptions-associated-directory.md) for details on the relationship between subscriptions and an Azure AD tenant.
