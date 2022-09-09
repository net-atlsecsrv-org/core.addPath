---
title: Prefer MSAL | Azure
description: Include file indicating that it's best to use MSAL. 
services: active-directory
author: hpsin
manager: CelesteDG

ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 11/17/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
---

This article describes how to program directly against the protocol in your application to request tokens from Azure AD.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](..\authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](..\sample-v2-code.md).
