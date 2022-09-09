---
title: Hybrid identity design - content management requirements Azure | Microsoft Docs
description: Provides insight into how to determine the content management requirements of your business. Usually when a user has their own device, they might also have multiple credentials that will be alternating according to the application that they use. It is important to differentiate what content was created using personal credentials versus the ones created using corporate credentials. Your identity solution should be able to interact with cloud services to provide a seamless experience to the end user while ensure their privacy and increase the protection against data leakage.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath

ms.collection: M365-identity-device-management
---
# Determine content management requirements for your hybrid identity solution
Understanding the content management requirements for your business may direct affect your decision on which hybrid identity solution to use. With the proliferation of multiple devices and the capability of users to bring their own devices ([BYOD](https://aka.ms/byodcg)), the company must protect its own data but it also must keep user’s privacy intact. Usually when a user has their own device, they might also have multiple credentials that will be alternating according to the application that they use. It is important to differentiate what content was created using personal credentials versus the ones created using corporate credentials. Your identity solution should be able to interact with cloud services to provide a seamless experience to the end user while ensure their privacy and increase the protection against data leakage. 

Your identity solution will be leveraged by different technical controls in order to provide content management as shown in the figure below:

![security controls](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Security controls that will be leveraging your identity management system**

In general, content management requirements will leverage your identity management system in the following areas:

* Privacy: identifying the user that owns a resource and applying the appropriate controls to maintain integrity.
* Data Classification: identify the user or group and level of access to an object according to its classification. 
* Data Leakage Protection: security controls responsible for protecting data to avoid leakage will need to interact with the identity system to validate the user’s identity. This is also important for auditing trail purpose.

> [!NOTE]
> Read [data classification for cloud readiness](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) for more information about best practices and guidelines for data classification.
> 
> 

When planning your hybrid identity solution ensure that the following questions are answered according to your organization’s requirements:

* Does your company have security controls in place to enforce data privacy?
  * If yes, will the security controls be able to integrate with the hybrid identity solution that you are going to adopt?
* Does your company use data classification?
  * If yes, is the current solution able to integrate with the hybrid identity solution that you are going to adopt?
* Does your company currently have any solution for data leakage? 
  * If yes, is the current solution able to integrate with the hybrid identity solution that you are going to adopt?
* Does your company need to audit access to resources?
  * If yes, what type of resources?
  * If yes, what level of information is necessary?
  * If yes, where the audit log must reside? On-premises or in the cloud?
* Does your company need to encrypt any emails that contain sensitive data (SSNs, credit card numbers, etc.)?
* Does your company need to encrypt all documents/contents shared with external business partners?
* Does your company need to enforce corporate policies on certain kinds of emails (do no reply all, do not forward)?

> [!NOTE]
> Make sure to take notes of each answer and understand the rationale behind the answer. [Define Data Protection Strategy](plan-hybrid-identity-design-considerations-data-protection-strategy.md) will go over the options available and advantages/disadvantages of each option.  By having answered those questions you will select which option best suits your business needs.
> 
> 

## Next steps
[Determine access control requirements](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## See Also
[Design considerations overview](plan-hybrid-identity-design-considerations-overview.md)

