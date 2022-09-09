---
title:  Unexpected error when performing consent to an application | Microsoft Docs
description: Discusses errors that can occur during the process of consenting to an application and what you can do about them
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: 
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
---

# Unexpected error when performing consent to an application

This article discusses errors that can occur during the process of consenting to an application. If you are troubleshooting unexpected consent prompts that do not contain any error messages, see [Authentication Scenarios for Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Many applications that integrate with Azure Active Directory require permissions to access other resources in order to function. When these resources are also integrated with Azure Active Directory, permissions to access them is often requested using the common consent framework. A consent prompt is displayed, which generally occurs the first time an application is used but can also occur on a subsequent use of the application.

Certain conditions must be true for a user to consent to the permissions an application requires. If these conditions are not met, the following errors can occur.

## Requesting not authorized permissions error
* **AADSTS90093:** &lt;clientAppDisplayName&gt; is requesting one or more permissions that you are not authorized to grant. Contact an administrator, who can consent to this application on your behalf.
* **AADSTS90094:** &lt;clientAppDisplayName&gt; needs permission to access resources in your organization that only an admin can grant. Please ask an admin to grant permission to this app before you can use it.

This error occurs when a user who is not a company administrator attempts to use an application that is requesting permissions that only an administrator can grant. This error can be resolved by an administrator granting access to the application on behalf of their organization.

This error can also occur when a user is prevented from consenting to an application due to Microsoft detecting that the permissions request is risky. In this case, an audit event will also be logged with a Category of "ApplicationManagement", Activity Type of "Consent to application" and Status Reason of "Risky application detected".

Another scenario in which this error might occur is when the user assignment is required for the application, but no administrator consent was provided. In this case, the administrator must first provide administrator consent.   

## Policy prevents granting permissions error
* **AADSTS90093:** An administrator of &lt;tenantDisplayName&gt; has set a policy that prevents you from granting &lt;name of app&gt; the permissions it is requesting. Contact an administrator of &lt;tenantDisplayName&gt;, who can grant permissions to this app on your behalf.

This error occurs when a company administrator turns off the ability for users to consent to applications, then a non-administrator user attempts to use an application that requires consent. This error can be resolved by an administrator granting access to the application on behalf of their organization.

## Intermittent problem error
* **AADSTS90090:** It looks like the sign-in process encountered an intermittent problem recording the permissions you attempted to grant to &lt;clientAppDisplayName&gt;. try again later.

This error indicates that an intermittent service side issue has occurred. It can be resolved by attempting to consent to the application again.

## Resource not available error
* **AADSTS65005:** The app &lt;clientAppDisplayName&gt; requested permissions to access a resource &lt;resourceAppDisplayName&gt; that is not available. 

Contact the application developer.

##  Resource not available in tenant error
* **AADSTS65005:** &lt;clientAppDisplayName&gt; is requesting access to a resource &lt;resourceAppDisplayName&gt; that is not available in your organization &lt;tenantDisplayName&gt;. 

Ensure that this resource is available or contact an administrator of &lt;tenantDisplayName&gt;.

## Permissions mismatch error
* **AADSTS65005:** The app requested consent to access resource &lt;resourceAppDisplayName&gt;. This request failed because it does not match how the app was pre-configured during app registration. Contact the app vendor.**

These errors all occur when the application a user is trying to consent to is requesting permissions to access a resource application that cannot be found in the organization’s directory (tenant). This situation can occur for several reasons:

-   The client application developer has configured their application incorrectly, causing it to request access to an invalid resource. In this case, the application developer must update the configuration of the client application to resolve this issue.

-   A Service Principal representing the target resource application does not exist in the organization, or existed in the past but has been removed. To resolve this issue, a Service Principal for the resource application must be provisioned in the organization so the client application can request permissions to it. The Service Principal can be provisioned in a number of ways, depending on the type of application, including:

    -   Acquiring a subscription for the resource application (Microsoft published applications)

    -   Consenting to the resource application

    -   Granting the application permissions via the Azure portal

    -   Adding the application from the Azure AD Application Gallery

## Risky app error and warning
* **AADSTS900941:** Administrator consent is required. App is considered risky. (AdminConsentRequiredDueToRiskyApp)
* This app may be risky. If you trust this app, please ask your admin to grant you access.
* **AADSTS900981:** An admin consent request was received for a risky app. (AdminConsentRequestRiskyAppWarning)
* This app may be risky. Only continue if you trust this app.

Both of these messages will be displayed when Microsoft has determined that the consent request may be risky. Among a number of other factors, this may occur if a [verified publisher](../develop/publisher-verification-overview.md) has not been added to the app registration. The first error code and message will be shown to end-users when the [Admin consent workflow](configure-admin-consent-workflow.md) is disabled. The second code and message will be shown to end-users when the admin consent workflow is enabled and to admins. 

End-users will not be able to grant consent to apps that have been detected as risky. Admins are able to, but should evaluate the app very carefuly and proceed with caution. If the app seems suspicious upon further review, it can be reported to Microsoft from the consent screen. 

## Next steps 

[Apps, permissions, and consent in Azure Active Directory (v1 endpoint)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Scopes, permissions, and consent in the Azure Active Directory (v2.0 endpoint)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


