---
title: Terms of Service and privacy statement for apps | Azure
description: Learn how you can configure the terms of service and privacy statement for apps registered to use Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''

ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
---

# How to: Configure terms of service and privacy statement for an app

Developers who build and manage apps that integrate with Azure Active Directory (Azure AD) and Microsoft accounts should include links to the app's terms of service and privacy statement. The terms of service and privacy statement are surfaced to users through the user consent experience. They help your users know that they can trust your app. The terms of service and privacy statement are especially critical for user-facing multi-tenant apps--apps that are used by multiple directories or are available to any Microsoft account.

You are responsible for creating the terms of service and privacy statement documents for your app, and for providing the URLs to these documents. For multi-tenant apps that fail to provide these links, the user consent experience for your app will show an alert, which may discourage users from consenting to your app.

> [!NOTE]
> * Single-tenant apps will not show an alert.
> * If one or both of the two links are missing, your app will show an alert.

## User consent experience

The following examples show the user consent experience when the terms of service and privacy statement are configured and when these links are not configured.

![Screenshots with and without a privacy statement and terms of service provided](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## Formatting links to the terms of service and privacy statement documents

Before you add links to your app's terms of service and privacy statement documents, make sure the URLs follow these guidelines.

| Guideline     | Description                           |
|---------------|---------------------------------------|
| Format        | Valid URL                             |
| Valid schemas | HTTP and HTTPS<br/>We recommend HTTPS |
| Max length    | 2048 characters                       |

Examples: `https://myapp.com/terms-of-service` and `https://myapp.com/privacy-statement`

## Adding links to the terms of service and privacy statement

When the terms of service and privacy statement are ready, you can add links to these documents in your app using one of these methods:

* [Through the Azure portal](#azure-portal)
* [Using the app object JSON](#app-object-json)
* [Using the MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Using the Azure portal
Follow these steps in the Azure portal.

1. Sign in to the [Azure portal](https://portal.azure.com/).
2. Navigate to the **App Registrations** section and select your app.
3. Open the **Branding** pane.
4. Fill out the **Terms of Service URL** and **Privacy Statement URL** fields.
5. Save your changes.

    ![App properties section with terms of service and privacy statement URLs](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Using the app object JSON

If you prefer to modify the app object JSON directly, you can use the manifest editor in the Azure portal or Application Registration Portal to include links to your app's terms of service and privacy statement.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Using the MSGraph beta REST API

To programmatically update all your apps, you can use the MSGraph beta REST API to update all your apps to include links to the terms of service and privacy statement documents.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Be careful not to overwrite any pre-existing values you have assigned to any of these fields: `supportUrl`, `marketingUrl`, and `logoUrl`
> * The MSGraph beta REST API will only work when you sign in with an Azure AD account. Personal Microsoft accounts are not supported.
