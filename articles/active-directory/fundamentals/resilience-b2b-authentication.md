---
title: Build resilience in external user authentication with Azure Active Directory
description: A guide for IT admins and architects to building resilient authentication for external users
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: "it-pro, seodec18"
ms.collection: M365-identity-device-management
---

# Build resilience in external user authentication

[Azure Active Directory B2B collaboration](https://docs.microsoft.com/azure/active-directory/external-identities/what-is-b2b) (Azure AD B2B) is a feature of [External Identities](https://docs.microsoft.com/azure/active-directory/external-identities/delegate-invitations) that enables collaboration with other organizations and individuals. It enables the secure onboarding of guest users into your Azure AD tenant without having to manage their credentials. External users bring their identity and credentials with them from an external identity provider (IdP), so they don’t have to remember a new credential. 

## Ways to authenticate external users

You can choose the methods of external user authentication to your directory. You can use Microsoft IdPs or other IdPs.

With every external IdP, you take a dependency on the availability of that IdP. With some methods of connecting to IdPs, there are things you can do to increase your resilience.

> [!NOTE] 
> Azure AD B2B has the built-in ability to authenticate any user from any [Azure Active Directory](https://docs.microsoft.com/azure/active-directory) tenant, or with a personal [Microsoft Account](https://account.microsoft.com/account). You do not have to do any configuration with these built-in options.

### Considerations for resilience with other IdPs

When using external IdPs for guest user authentication, there are certain configurations that you must ensure you maintain to prevent disruptions.

| Authentication Method| Resilience considerations |
| - | - |
| Federation with social IDPs like [Facebook](https://docs.microsoft.com/azure/active-directory/external-identities/facebook-federation) or [Google](https://docs.microsoft.com/azure/active-directory/external-identities/google-federation).| You must maintain your account with the IdP and configure your Client ID and Client Secret. |
| [Direct Federation with SAML and WS-Federation Identity Providers](https://docs.microsoft.com/azure/active-directory/external-identities/direct-federation)| You must collaborate with the IdP owner for access to their endpoints, upon which you're dependent. <br>You must maintain the metadata that contain the certificates and endpoints. |
| [Email one-time passcode](https://docs.microsoft.com/azure/active-directory/external-identities/one-time-passcode)| With this method you're dependent on Microsoft’s email system, the user’s email system, and the user’s email client. |


 

## Self-service sign-up (preview)

As an alternative to sending invitations or links, you can enable [Self-service sign-up](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-overview).  This allows external users to request access to an application. You must create an [API connector](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) and associate it with a user flow. You associate user flows that define the user experience with one or more applications. 

It’s possible to use [API connectors](https://docs.microsoft.com/azure/active-directory/external-identities/api-connectors-overview) to integrate your self-service sign-up user flow with external systems’ APIs. This API integration can be used for [custom approval workflows](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-approvals), [performing identity verification](https://docs.microsoft.com/azure/active-directory/external-identities/code-samples-self-service-sign-up), and other tasks such as overwriting user attributes. Using APIs requires that you manage the following dependencies.

* **API Connector Authentication**: Setting up a connector requires an endpoint URL, a username, and a password. Set up a process by which these credentials are maintained, and work with the API owner to ensure you know any expiration schedule.

* **API Connector Response**: Design API Connectors in the sign-up flow to fail gracefully if the API isn't available. Examine and provide to your API developers these [example API responses](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector) and the [best practices for troubleshooting](https://docs.microsoft.com/azure/active-directory/external-identities/self-service-sign-up-add-api-connector). Work with the API development team to test all possible response scenarios, including continuation, validation-error, and blocking responses. 

## Next steps
Resilience resources for administrators and architects
 
* [Build resilience with credential management](resilience-in-credentials.md)

* [Build resilience with device states](resilience-with-device-states.md)

* [Build resilience by using Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Build resilience in your hybrid authentication](resilience-in-hybrid.md)

* [Build resilience in application access with Application Proxy](resilience-on-premises-access.md)

Resilience resources for developers

* [Build IAM resilience in your applications](resilience-app-development-overview.md)

* [Build resilience in your CIAM systems](resilience-b2c.md)
 
