---
title: How Azure AD uses the SAML protocol
description: This article provides an overview of the Single Sign-On and Single Sign-Out SAML profiles in Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
---

# How Azure AD uses the SAML protocol

Azure Active Directory (Azure AD) uses the SAML 2.0 protocol to enable applications to provide a single sign-on experience to their users. The [Single Sign-On](single-sign-on-saml-protocol.md) and [Single Sign-Out](single-sign-out-saml-protocol.md) SAML profiles of Azure AD explain how SAML assertions, protocols, and bindings are used in the identity provider service.

SAML Protocol requires the identity provider (Azure AD) and the service provider (the application) to exchange information about themselves.

When an application is registered with Azure AD, the app developer registers federation-related information with Azure AD. This information includes the **Redirect URI** and **Metadata URI** of the application.

Azure AD uses the cloud service's **Metadata URI** to retrieve the signing key and the logout URI. Customer can open the app in **Azure AD -> App Registration** and then in **Settings -> Properties**, they can update the Logout URL. This way Azure AD can send the response to the correct URL. 

Azure Active Directory exposes tenant-specific and common (tenant-independent) single sign-on and single sign-out endpoints. These URLs represent addressable locations -- they are not just identifiers -- so you can go to the endpoint to read the metadata.

* The tenant-specific endpoint is located at `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. The *\<TenantDomainName>* placeholder represents a registered domain name or TenantID GUID of an Azure AD tenant. For example, the federation metadata of the contoso.com tenant is at: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* The tenant-independent endpoint is located at
  `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. In this endpoint address, **common** appears instead of a tenant domain name or ID.

For information about the federation metadata documents that Azure AD publishes, see [Federation Metadata](../azuread-dev/azure-ad-federation-metadata.md).
