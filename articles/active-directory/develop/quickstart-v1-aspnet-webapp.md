---
title: Add sign-in with Microsoft to an ASP.NET web app | Microsoft Docs
description: Learn how to add Microsoft sign in on an ASP.NET solution with a traditional web browser-based application using OpenID Connect standard.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''

ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev 
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
#Customer intent: As an application developer, I want to learn how to implement Microsoft sign-in with an ASP.NET solution with a browser-based app using the OpenID Connect standard.
ms.collection: M365-identity-device-management
---

# Quickstart: Add sign-in with Microsoft to an ASP.NET web app

[Microsoft identity platform](v2-overview.md) is an evolution of the Azure Active Directory (Azure AD) developer platform. It allows developers to build applications that sign in all Microsoft identities and get tokens to call Microsoft APIs such as Microsoft Graph or APIs that developers have built.

[Microsoft Authentication Library (MSAL)](msal-overview.md) enables developers to acquire tokens from the Microsoft identity platform endpoint in order to access secured Web APIs. Active Directory Authentication Library (ADAL) integrates with the Azure AD for developers (v1.0) endpoint, where MSAL integrates with the Microsoft identity platform (v2.0) endpoint.

For new web applications, we recommend you use Microsoft identity platform (v2.0) and MSAL to acquire tokens and access secured web APIs: [Quickstart: Add sign-in with Microsoft to an ASP.NET web app](quickstart-v2-aspnet-webapp.md).