---
title: Build resilience in application access with Application Proxy
description: A guide for architects and IT administrators on using Application Proxy for resilient access to on-premises applications
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

# Build resilience in application access with Application Proxy

Application Proxy is a feature of Azure AD that enables users to access on-premises web applications from a remote client. Application Proxy includes both the Application Proxy service in the cloud, and the Application Proxy connectors, which run on an on-premises server. 

Users access on-premises resources through a URL published via Application Proxy. They are redirected to the Azure AD sign in page. The Application Proxy service in Azure AD then sends a token to the Application Proxy connector in the corporate network, which passes the token to the on-premises Active Directory The authenticated user can then access the on-premises resource. In the diagram below, [connectors](../manage-apps/application-proxy-connectors.md) are shown in a [connector group](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> When you publish your applications via Application Proxy, you must implement [capacity planning and appropriate redundancy for the Application Proxy connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning).

![Architecture diagram of Application y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## How do I implement Application Proxy?

To implement remote access with Azure AD Application Proxy, see the following resources.

* [Planning an Application Proxy deployment](../manage-apps/application-proxy-deployment-plan.md)

* [High availability and load balancing best practices](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Configure proxy servers](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Design a resilient access control strategy](../authentication/concept-resilient-controls.md)

## Next steps
Resilience resources for administrators and architects
 
* [Build resilience with credential management](resilience-in-credentials.md)

* [Build resilience with device states](resilience-with-device-states.md)

* [Build resilience by using Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Build resilience in external user authentication](resilience-b2b-authentication.md)

* [Build resilience in your hybrid authentication](resilience-in-hybrid.md)

Resilience resources for developers

* [Build IAM resilience in your applications](resilience-app-development-overview.md)

* [Build resilience in your CIAM systems](resilience-b2c.md)
