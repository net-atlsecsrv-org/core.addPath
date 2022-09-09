---
title: Enable automatic user provisioning for Azure Active Directory multi-tenant applications
description: A guide for independent software vendors for enabling automated provisioning
services: active-directory
documentationcenter: 'azure'
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia

ms.collection: active-directory
---
# Enable automatic user provisioning for your multi-tenant application

Automatic user provisioning is the process of automating the creation, maintenance, and removal of user identities in target systems like your software-as-a-service applications.

## Why enable automatic user provisioning?

Applications that require that a user record is present in the application before a user’s first sign in require user provisioning. There are benefits to you as a service provider, and benefits to your customers.

### Benefits to you as the service provider

* Increase the security of your application by using the Microsoft identity platform.

* Reduce actual and perceived customer effort to adopt your application.

* Reduce your costs in integrating with multiple identity providers (IdPs) for automatic user provisioning by using System for Cross-Domain Identity Management (SCIM)-based provisioning.

* Reduce support costs by providing rich logs to help customers troubleshoot user provisioning issues.

* Increase the visibility of your application in the [Azure AD app gallery](https://azuremarketplace.microsoft.com/marketplace/apps).

* Get a prioritized listing in the App Tutorials page.

### Benefits to your customers

* Increase security by automatically removing access to your application for users who change roles or leave the organization to your application.

* Simplify user management for your application by avoiding human error and repetitive work associated with manual provisioning.

* Reduce the costs of hosting and maintaining custom-developed provisioning solutions.

## Choose a provisioning method

Azure AD provides several integration paths to enable automatic user provisioning for your application.

* The [Azure AD Provisioning Service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) manages the provisioning and deprovisioning of users from Azure AD to your application (outbound provisioning) and from your application to Azure AD (inbound provisioning). The service connects to the System for Cross-Domain Identity Management (SCIM) user management API endpoints provided by your application.

* When using the [Microsoft Graph](https://docs.microsoft.com/graph/), your application manages inbound and outbound provisioning of users and groups from Azure AD to your application by querying the Microsoft Graph API.

* The Security Assertion Markup Language Just in Time (SAML JIT) user provisioning can be enabled if your application is using SAML for federation. It uses claims information sent in the SAML token to provision users.

To help determine which integration option to use for your application, refer to the high-level comparison table, and then see the more detailed information on each option.

| Capabilities enabled or enhanced by Automatic Provisioning| Azure AD Provisioning Service (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| User and group management in Azure AD| √| √| User only |
| Manage users and groups synced from on-premises Active Directory| √*| √*| User only* |
| Access data beyond users and groups during provisioning Access to O365 data (Teams, SharePoint, Email, Calendar, Documents, etc.)| X+| √| X |
| Create, read, and update users based on business rules| √| √| √ |
| Delete users based on business rules| √| √| X |
| Manage automatic user provisioning for all applications from the Azure portal| √| X| √ |
| Support multiple identity providers| √| X| √ |
| Support guest accounts (B2B)| √| √| √ |
| Support non-enterprise accounts (B2C)| X| √| √ |

<sup>*</sup> – Azure AD Connect setup is required to sync users from AD to Azure AD.  
<sup>+</sup >– Using SCIM for provisioning does not preclude you from integrating your application with MIcrosoft Graph for other purposes.

## Azure AD Provisioning Service

The Azure AD provisioning services uses SCIM, a protocol supported by many identity providers (IdPs). We recommend you use the Azure AD provisioning service if you want to support IdPs in addition to Azure AD, as any SCIM-compliant IdP can connect to your SCIM endpoint.

For more information on how the Azure AD Provisioning Service users SCIM, see: 

* [Using System for Cross-Domain Identity Management (SCIM) to automatically provision users and groups from Azure Active Directory to applications](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Understand the Azure AD SCIM implementation](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Build a SCIM endpoint using Microsoft CLI Libraries](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

## Microsoft Graph for Provisioning

When you use Microsoft Graph for provisioning, you have access to all the rich user data available in Graph. In addition to the details of users and groups, you can also fetch additional information like the user’s roles, manager and direct reports, owned and registered devices, and hundreds of other data pieces available in the [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0). 

More than 15 million organizations, and 90% of fortune 500 companies use Azure AD while subscribing to Microsoft cloud services like Office 365, Microsoft Azure, Enterprise Mobility Suite, or Microsoft 365. You can use Microsoft Graph to integrate your app with administrative workflows, such as employee onboarding (and termination), profile maintenance, and more. 

Learn more about using Microsoft Graph for provisioning:

* [Microsoft Graph Home page](https://developer.microsoft.com/graph)

* [Overview of Microsoft Graph](https://docs.microsoft.com/graph/overview)

* [Microsoft Graph Auth Overview](https://docs.microsoft.com/graph/auth/)

* [Getting started with Microsoft Graph](https://developer.microsoft.com/graph/get-started)

* [Use the Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)

* [Microsoft Graph Explorer](https://aka.ms/ge) 

* [Microsoft Graph permissions reference](https://docs.microsoft.com/graph/permissions-reference)

* [Best practices for working with Microsoft Graph](https://docs.microsoft.com/graph/best-practices-concept)

* [Featured scenarios for Microsoft Graph](https://developer.microsoft.com/graph/examples)

## Using SAML JIT for provisioning

If you want to provision users only upon first sign in to your application, and do not need to automatically deprovision users, SAML JIT is an option. Your application must support SAML 2.0 as a federation protocol to use SAML JIT.

SAML JIT uses the claims information in the SAML token to create and update user information in the application. Customers can configure these required claims in the Azure AD application as needed. Sometimes the JIT provisioning needs to be enabled from the application side so that customer can use this feature. SAML JIT is useful for creating and updating users, but it can't delete or deactivate the users in the application.

## Next Steps

* [Enable Single Sign-on for your application](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-sso-content)

* [Submit your application listing](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) and partner with Microsoft to create documentation on Microsoft’s site.

* [Join the Microsoft Partner Network (free) and create your go to market plan](https://partner.microsoft.com/en-us/explore/commercial).
