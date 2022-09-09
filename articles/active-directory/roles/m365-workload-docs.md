---
title: Admin role docs across Microsoft 365 services - Azure AD | Microsoft Docs
description: Find content and API references for administrator roles for Microsoft 365 services in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
#As an Azure AD administrator, to delegate permissions across Microsoft 365 services quickly and accurately I want to know where the content is for admin roles.
ms.collection: M365-identity-device-management
---

# Administrator roles for Microsoft 365 services

All products in Microsoft 365 can be managed with administrative roles in Azure AD. Some products also provide additional roles that are specific to that product. For information on the roles supported by each product, see the table below. General discussions of delegation issues can be found in [Role delegation planning in Azure Active Directory](concept-delegation.md).

## Where to find content

Microsoft 365 service | Role content | API content
---------------------- | ------------------ | -----------------
Admin roles in Office 365 and Microsoft 365 business plans | [Microsoft 365 admin roles](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Not available
Azure Active Directory (Azure AD) and Azure AD Identity Protection| [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Exchange role-based access control](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell for Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Fetch role assignments](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD admin roles](permissions-reference.md)<br>Also [About the SharePoint admin role in Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype for Business | [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & Compliance Center (Office 365 Advanced Threat Protection, Exchange Online Protection, Information Protection) | [Office 365 admin roles](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Fetch role assignments](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Secure Score | [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Compliance Manager roles](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Not available
Azure Information Protection | [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Role-based access control](/cloud-app-security/manage-admins) | [API reference](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP role groups](/azure-advanced-threat-protection/atp-role-groups) | Not available
Windows Defender Advanced Threat Protection | [Windows Defender ATP role-based access control](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Not available
Privileged Identity Management | [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune role-based access control](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Fetch role assignments](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Managed Desktop | [Azure AD admin roles](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Fetch role assignments](/graph/api/directoryrole-list?view=graph-rest-1.0)

## Next steps

* [How to assign or remove Azure AD administrator roles](manage-roles-portal.md)
* [Azure AD administrator roles reference](permissions-reference.md)