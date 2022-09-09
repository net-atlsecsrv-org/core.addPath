---
title: Problems signing in to an custom-developed application | Microsoft Docs
description: Common errors that could be causing you to not be able to sign into an application you have developed with Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG

ms.assetid:
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen

ms.collection: M365-identity-device-management
---

# Problems signing in to a custom-developed application

There are several errors that could be causing you to not be able to sign into an app. The biggest reason people encounter this problem is misconfigured apps.

## Errors related to  misconfigured apps

* Verify both the configurations in the portal match what you have in your app. Specifically, compare Client/Application ID, Reply URLs, Client Secrets/Keys, and App ID URI.

* Compare the resource you’re requesting access to in code with the configured permissions in the **Required Resources** tab to make sure you only request resources you’ve configured.

* See [Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory) for any similar errors or problems.

## Next steps

[Azure AD Developer Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consent and Integrating Apps to Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Permissions and consent in the Microsoft identity platform endpoint](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
