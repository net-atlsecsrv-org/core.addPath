---
title: SAML authentication with Azure Active Directory
description: Architectural guidance on achieving this authentication pattern
services: active-directory
author: BarbaraSelden
manager: daveba

ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: "it-pro, seodec18"
ms.collection: M365-identity-device-management
---

# SAML authentication with Azure Active Directory

Security Assertion Markup Language (SAML) is an open standard for exchanging authentication and authorization data between an identity provider and a service provider. SAML is an XML-based markup language for security assertions, which are statements that service providers use to make access-control decisions. 

The SAML specification defines three roles:

* The principal, generally a user
* The identity provider (IdP)
* The  service provider (SP)


## Use when

There's a need to provide an single sign-on (SSO) experience for an enterprise SAML application.

While one of most important use cases that SAML addresses is SSO, especially by extending SSO across security domains, there are other use cases (called profiles) as well. 

![architectural diagram](./media/authentication-patterns/saml-auth.png)

## Components of system

* **User**: Requests a service from the application.

* **Web browser**: The component that the user interacts with.

* **Web app**: Enterprise application that supports SAML and uses Azure AD as IdP.

* **Token**: A SAML assertion (also known as SAML tokens) that carries sets of claims made by the IdP about the principle (user). It contains authentication information, attributes, and authorization decision statements.

* **Azure AD**: Enterprise cloud IdP that provides SSO and Multi-factor authentication for SAML apps. It synchronizes, maintains, and manages identity information for users while providing authentication services to relying applications. 

## Implement SAML authentication with Azure AD

* [Tutorials for integrating SaaS applications using Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Configuring SAML based single sign-on for non-gallery applications](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [How Azure AD uses the SAML protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
