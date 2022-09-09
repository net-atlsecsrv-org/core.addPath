---
title: 'Azure AD Pass-through Authentication: Version release history | Microsoft Docs'
description: This article lists all releases of the Azure AD Pass-through Authentication agent
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Azure AD Pass-through Authentication agent: Version release history 
 
The agents installed on-premises that enable Pass-through Authentication are updated regularly to provide new capabilities. This article lists the versions and features that are added when new functionality is introduced. Pass-through authentication agents are updated automatically when a new version is released. 

Here are related topics: 

- [User sign-in with Azure AD Pass-through Authentication](how-to-connect-pta.md) 
- [Azure AD Pass-through Authentication agent installation](how-to-connect-pta-quick-start.md) 



## 1.5.1007.0 
### Release status 
1/22/2019: Released for download  
### New features and improvements 
- Added support for Service Bus reliable channels to add another layer of connection resiliency for outbound connections 
- Enforce TLS 1.2 during agent registration 

## 1.5.643.0 
### Release status 
4/10/2018: Released for download  
### New features and improvements 
- Web socket connection support 
- Set TLS 1.2 as the default protocol for the agent 
 
## 1.5.405.0 
### Release status 
1/31/2018: Released for download  
### Fixed issues 

- Fixed a bug that caused some memory leaks in the agent. 
- Updated the Azure Service Bus version, which includes a bug fix for connector timeout issues. 
 
## 1.5.405.0 
### Release status 
11/26/2017: Released for download  
### New features and improvements 
- Added support for websocket based connections between the agent and Azure AD services to improve connection resiliency 

## 1.5.402.0 
### Release status 
11/25/2017: Released for download  
### Fixed issues 
- Fixed bugs related to the DNS cache for default proxy scenarios 
 
## 1.5.389.0 
### Release status 
10/17/2017: Released for download  
### New features and improvements 
- Added DNS cache functionality for outbound connections to add resiliency from DNS failures 
 
## 1.5.261.0 
### Release status 
08/31/2017: Released for download  
### New features and improvements 
- GA version of the Azure AD Pass-through authentication agent 

## Next steps

- [User sign-in with Azure Active Directory Pass-through Authentication](how-to-connect-pta.md)