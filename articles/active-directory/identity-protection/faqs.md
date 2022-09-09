---
title: Azure Active Directory Identity Protection FAQ | Microsoft Docs
description: 'Frequently asked questions about Azure AD Identity Protection'

services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle

ms.collection: M365-identity-device-management
---
# Azure Active Directory Identity Protection FAQ

This article includes answers to frequently asked questions about Azure Active Directory (Azure AD) Identity Protection. For more information, see [Azure Active Directory Identity Protection](../active-directory-identityprotection.md). 

## Why do some risk detections have “Closed (system)” status?

**A:** These risk detections were detected by Identity Protection and later closed because the events were no longer considered risky. These events do not count towards the user’s risk level. 

---

## Do I need to be a global admin to use Identity Protection in the Azure portal?
**A:** No. You can either be a Security Reader, a Security Admin or a Global Admin to use Identity Protection.

---

## How do I get Identity Protection?

**A:** See [Getting started with Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) for an answer to this question.

---

## How can I sort users in "Users flagged for risk"?

**A:** Download the users flagged for risk report by clicking **Download** on the top of the **Users flagged for risk** page. You can then sort the downloaded data based on available fields, including Last Updated (UTC).

---
