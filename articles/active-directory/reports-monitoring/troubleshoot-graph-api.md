---

title: 'Troubleshoot errors in Azure Active Directory reporting API | Microsoft Docs'
description: Provides you with a resolution to errors while calling Azure Active Directory Reporting APIs.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''

ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk

ms.collection: M365-identity-device-management
---

# Troubleshoot errors in Azure Active Directory reporting API

This article lists the common error messages you may run into while accessing activity reports using the Microsoft Graph API and steps for their resolution.

### 500 HTTP internal server error while accessing Microsoft Graph V2 endpoint

We do not currently support the Microsoft Graph v2 endpoint - make sure to access the activity logs using the Microsoft Graph v1 endpoint.

### Error: Neither tenant is B2C or tenant doesn't have premium license

Accessing sign-in reports requires an Azure Active Directory premium 1 (P1) license. If you see this error message while accessing sign-ins, make sure that your tenant is licensed with an Azure AD P1 license.

### Error: User is not in the allowed roles 

If you see this error message while trying to access audit logs or sign-ins using the API, make sure that your account is part of the **Security Reader** or **Report Reader** role in your Azure Active Directory tenant. 

### Error: Application missing AAD 'Read directory data' permission 

Please follow the steps in the [Prerequisites to access the Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) to ensure your application is running with the right set of permissions. 

### Error: Application missing Microsoft Graph API 'Read all audit log data' permission

Please follow the steps in the [Prerequisites to access the Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) to ensure your application is running with the right set of permissions. 

## Next Steps

[Use the audit API reference](/graph/api/resources/directoryaudit?view=graph-rest-beta)
[Use the sign-in activity report API reference](/graph/api/resources/signin?view=graph-rest-beta)