---
title: Hybrid identity design - multi-factor authentication requirements Azure | Microsoft Docs
description: With Conditional Access control, Azure AD verifies the specific conditions you pick when authenticating the user and before allowing access to the application.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''

ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
---
# Determine multi-factor authentication requirements for your hybrid identity solution
In this world of mobility, with users accessing data and applications in the cloud and from any device, securing this information has become paramount.  Every day there is a new headline about a security breach.  Although, there is no guarantee against such breaches, multi-factor authentication, provides an additional layer of security to help prevent these breaches.
Start by evaluating the organizations requirements for multi-factor authentication. That is, what is the organization trying to secure.  This evaluation is important to define the technical requirements for setting up and enabling the organizations users for multi-factor authentication.

Make sure to answer the following:

* Is your company trying to secure Microsoft apps? 
* How these apps are published?
* Does your company provide remote access to allow employees to access on-premises apps?

If yes, what type of remote access?You also need to evaluate where the users who are accessing these applications will be located. This evaluation is another important step to define the proper multi-factor authentication strategy. Make sure to answer the following questions:

* Where are the users going to be located?
* Can they be located anywhere?
* Does your company want to establish restrictions according to the user’s location?

Once you understand these requirements, it is important to also evaluate the user’s requirements for multi-factor authentication. This evaluation is important because it will define the requirements for rolling out multi-factor authentication. Make sure to answer the following questions:

* Are the users familiar with multi-factor authentication?
* Will some uses be required to provide additional authentication?  
  * If yes, all the time, when coming from external networks, or accessing specific applications, or under other conditions?
* Will the users require training on how to setup and implement multi-factor authentication?
* What are the key scenarios that your company wants to enable multi-factor authentication for their users?

After answering the previous questions, you will be able to understand if there are multi-factor authentication already implemented on-premises. This evaluation is important to define the technical requirements for setting up and enabling the organizations users for multi-factor authentication. Make sure to answer the following questions:

* Does your company need to protect privileged accounts with MFA?
* Does your company need to enable MFA for certain application for compliance reasons?
* Does your company need to enable MFA for all eligible users of these application or only administrators?
* Do you need have MFA always enabled or only when the users are logged outside of your corporate network?

## Next steps
[Define a hybrid identity adoption strategy](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## See also
[Design considerations overview](plan-hybrid-identity-design-considerations-overview.md)

