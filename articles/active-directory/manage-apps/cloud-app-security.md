---
title: App visibility and control with Microsoft Cloud App Security 
description: Learn ways to identify app risk levels, stop breaches and leaks in real time, and use app connectors to take advantage of provider APIs for visibility and governance.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
---

# Cloud app visibility and control

To get the full benefit of cloud apps and services, an IT team must find the right balance of supporting access while maintaining control to protect critical data. Microsoft Cloud App Security provides rich visibility, control over data travel, and sophisticated analytics to identify and combat cyber threats across all your Microsoft and third-party cloud services.

## Discover and manage shadow IT in your network

When IT admins are asked how many cloud apps they think their employees use, on average they say 30 or 40, when in reality, the average is over 1,000 separate apps being used by employees in your organization. Shadow IT helps you know and identify which apps are being used and what your risk level is. Eighty percent of employees use unsanctioned apps that no one has reviewed and may not be compliant with your security and compliance policies. And because your employees are able to access your resources and apps from outside your corporate network, it's no longer enough to have rules and policies on your firewalls.

Use Microsoft Cloud App Discovery (an Azure Active Directory Premium P1 feature) to discover which apps are being used, explore the risk of these apps, configure policies to identify new risky apps, and unsanction these apps in order to block them natively using your proxy or firewall appliance.

- Discover and identify Shadow IT
- Evaluate and analyze
- Manage your apps
- Advanced Shadow IT discovery reporting
- Control sanctioned apps
 
### Learn more

- [Discover and manage shadow IT in your network ](/cloud-app-security/tutorial-shadow-it)
- [Discovered apps with Cloud App Security ](/cloud-app-security/discovered-apps)
 
## User session visibility and control 

In today’s workplace, it’s often not enough to know what’s happening in your cloud environment after the fact. You want to stop breaches and leaks in real time before employees intentionally or inadvertently put your data and your organization at risk. Together with Azure Active Directory (Azure AD), Microsoft Cloud App Security delivers these capabilities in a holistic and integrated experience with Conditional Access App Control. 

Session control uses a reverse proxy architecture and is uniquely integrated with Azure AD Conditional Access. Azure AD Conditional Access allows you to enforce access controls on your organization’s apps based on certain conditions. The conditions define who (user or group of users) and what (which cloud apps) and where (which locations and networks) a Conditional Access policy is applied to. After you’ve determined the conditions, you can route users to Cloud App Security where you can protect data in real time.  

With this control you can:  
- Control file downloads
- Monitor B2B scenarios  
- Control access to files  
- Protect documents on download  
 
### Learn more

- [Protect apps with Session Control in Cloud App Security ](/cloud-app-security/proxy-intro-aad)
 
## Advanced app visibility and controls 

App connectors use the APIs of app providers to enable greater visibility and control by Microsoft Cloud App Security over the apps you connect to. 
Cloud App Security leverages the APIs provided by the cloud provider. Each service has its own framework and API limitations such as throttling, API limits, dynamic time-shifting API windows, and others. The Cloud App Security product team worked with these services to optimize the use of APIs and provide the best performance. Taking into account different limitations services impose on their APIs, the Cloud App Security engines use their maximum allowed capacity. Some operations, such as scanning all files in the tenant, require numerous API calls so they're spread over a longer period. Expect some policies to run for several hours or days. 
 
### Learn more  

- [Connect apps in Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## Next steps

- [Discover and manage shadow IT in your network ](/cloud-app-security/tutorial-shadow-it)
- [Discovered apps with Cloud App Security ](/cloud-app-security/discovered-apps)
- [Protect apps with Session Control in Cloud App Security ](/cloud-app-security/proxy-intro-aad)
- [Connect apps in Cloud App Security ](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)