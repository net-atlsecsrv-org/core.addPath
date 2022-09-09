---
title: What are service dependencies in Azure Active Directory conditional access? | Microsoft Docs
description: Learn how conditions are used in Azure Active Directory conditional access to trigger a policy.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''

ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2019
ms.author: joflore
ms.reviewer: calebb

#Customer intent: As an IT admin, I need to understand what service dependencies are in conditional access so that I can assess how policies are applied

ms.collection: M365-identity-device-management
---

# What are service dependencies in Azure Active Directory conditional access? 


With conditional access policies, you can specify access requirements to websites and services. For example, your access requirements can include requiring multi-factor authentication (MFA) or [managed devices](require-managed-devices.md). 


When you access a site or service directly, the impact of a related policy is typically easy to assess. For example, if you have a policy that requires MFA for SharePoint Online configured, MFA is enforced for each sign-in to the SharePoint web portal. However, it is not always straight-forward to assess the impact of a policy because there are cloud apps with dependencies to other cloud apps. For example, Microsoft Teams leverages SharePoint online. So, when you access Microsoft Teams in our current scenario, you are also subject to the SharePoint MFA policy.   


## Policy enforcement 

If you have a service dependency configured, the policy may be applied using early-bound or late-bound enforcement. 

**Early-bound policy enforcement** means a user must satisfy the dependent service policy before accessing the calling app. For example, a user must satisfy SharePoint policy before signing into MS Teams. 

**Late-bound policy enforcement** occurs after the user signs into the calling app. Enforcement is deferred to when calling app requests, a token for the downstream service. Examples include MS Teams accessing Planner and Office.com accessing SharePoint. 

The diagram below illustrates MS Teams service dependencies. Solid arrows indicate early-bound enforcement the dashed arrow for Planner indicates late-bound enforcement. 



![MS Teams service dependencies](./media/service-dependencies/01.png)



  

As a best practice, you should set common policies across related apps and services whenever possible. Having a consistent security posture provides you with the best user experience. For example, setting a common policy across Exchange Online, SharePoint Online, MS Teams and Skype for business significantly reduces unexpected prompts that may arise from different policies being applied to downstream services. 

The below table lists additional service dependencies, where the client apps must satisfy  

| Client apps         | Downstream service                          | Enforcement |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal and API) | Early-bound |
| Microsoft Classroom | Exchange                                    | Early-bound |
|                     | SharePoint                                  | Early-bound  |
| Microsoft Teams     | Exchange                                    | Early-bound |
|                     | MS Planner                                  | Late-bound  |
|                     | SharePoint                                  | Early-bound |
|                     | Skype for Business Online                   | Early-bound |
| Office Portal       | Exchange                                    | Late-bound  |
|                     | SharePoint                                  | Late-bound  |
| Outlook groups      | Exchange                                    | Early-bound |
|                     | SharePoint                                  | Early-bound |
| PowerApps           | Microsoft Azure Management (portal and API) | Early-bound |
|                     | Windows Azure Active Directory              | Early-bound |
| Project             | Dynamics CRM                                | Early-bound |
| Skype for Business  | Exchange                                    | Early-bound |
| Visual Studio       | Microsoft Azure Management (portal and API) | Early-bound |



## Next steps

To learn how to implement conditional access in your environment, see [Plan your conditional access deployment in Azure Active Directory](plan-conditional-access.md).
