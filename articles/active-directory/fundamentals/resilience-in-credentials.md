---
title: Build resilience with credential management in Azure Active Directory
description: A guide for architects
 and IT administrators on building a resilient credential strategy.
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

# Build resilience with credential management

When a credential is presented to Azure AD in a token request, there are multiple dependencies that must be available for validation. The first authentication factor relies on Azure AD authentication, and in some cases on on-premises infrastructure. For more information on hybrid authentication architectures, see [Build resilience in your hybrid infrastructure](resilience-in-hybrid.md). 

If you implement a second factor, the dependencies for the second factor are added to the dependencies for the first. For example, if your first factor is via PTA, and your second factor is SMS, your dependencies are:

* Azure AD authentication services

* Azure MFA service

* On-premises infrastructure

* Phone carrier

* The user’s device (not pictured)

 
![Image of authentication methods and dependencies](./media/resilience-in-credentials/admin-resilience-credentials.png)

Your credential strategy should consider the dependencies of each authentication type, and provision methods that avoid a single point of failure. 

Because authentication methods have different dependencies, it’s a good idea to enable users to register for as many second-factor options as possible. Be sure to include second factors with different dependencies if possible. For example, Voice call and SMS as second factors share the same dependencies, so having them as the only options does not mitigate risk.

The most resilient credential strategy is to use passwordless authentication. Windows Hello for Business and FIDO 2.0 security keys have fewer dependencies than strong authentication with two separate factors. The Microsoft Authenticator app, Windows Hello for Business and Fido 2.0 security keys are the most secure. 

For second factors, the Microsoft Authenticator app or other authenticator apps using time-based one time passcode (TOTP) or OATH hardware tokens have the fewest dependencies, and are therefore more resilient.

## How do multiple credentials help resilience?

Provisioning multiple credential types gives users options that accommodate their preferences and environmental constraints. As a result, interactive authentication where users are prompted for Multi-factor authentication will be more resilient to specific dependencies being unavailable at the time of the request. You can [optimize reauthentication prompts for Multi-factor authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

In addition to individual user resiliency described above, enterprises should plan contingencies for large-scale disruptions such as operational errors that introduce a misconfiguration, a natural disaster, or an enterprise-wide resource outage to an on-premises federation service (especially when used for Multi-factor authentication). 

## How do I implement resilient credentials?

* Deploy [Passwordless credentials](../authentication/howto-authentication-passwordless-deployment.md) such as Windows Hello for Business, Phone Authentication, and FIDO2 security keys to reduce dependencies.

* Deploy the [Microsoft Authenticator App](../user-help/user-help-auth-app-overview.md) as a second factor.

* Turn on [password hash synchronization](../hybrid/whatis-phs.md) for hybrid accounts that are synchronized from Windows Server Active Directory. This option can be enabled alongside federation services such as AD FS and provides a fall back in case the federation service fails.

* [Analyze usage of Multi-factor authentication methods](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) to improve users’ experience.

* [Implement a resilient access control strategy](../authentication/concept-resilient-controls.md)

## Next steps
Resilience resources for administrators and architects
 
* [Build resilience with device states](resilience-with-device-states.md)

* [Build resilience by using Continuous Access Evaluation (CAE)](resilience-with-continuous-access-evaluation.md)

* [Build resilience in external user authentication](resilience-b2b-authentication.md)

* [Build resilience in your hybrid authentication](resilience-in-hybrid.md)

* [Build resilience in application access with Application Proxy](resilience-on-premises-access.md)

Resilience resources for developers

* [Build IAM resilience in your applications](resilience-app-development-overview.md)

* [Build resilience in your CIAM systems](resilience-b2c.md)
