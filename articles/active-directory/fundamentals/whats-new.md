---
title: What's new? Release notes - Azure Active Directory | Microsoft Docs
description: Learn what is new with Azure Active Directory, such as the latest release notes, known issues, bug fixes, deprecated functionality, and upcoming changes.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
 - clicktale
 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
---

# What's new in Azure Active Directory?

>Get notified about when to revisit this page for updates by copying and pasting this URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` into your ![RSS feed reader icon](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD receives improvements on an ongoing basis. To stay up to date with the most recent developments, this article provides you with information about:

- The latest releases
- Known issues
- Bug fixes
- Deprecated functionality
- Plans for changes

This page is updated monthly, so revisit it regularly. If you're looking for items that are older than six months, you can find them in the [Archive for What's new in Azure Active Directory](whats-new-archive.md).

---

## April 2020

### Combined security info registration experience is now generally available

**Type:** New feature

**Service category:** Authentications (Logins)

**Product capability:** Identity Security & Protection

The combined registration experience for Multi-Factor Authentication (MFA) and Self-Service Password Reset (SSPR) is now generally available. This new registration experience enables users to register for MFA and SSPR in a single, step-by-step process. When you deploy the new experience for your organization, users can register in less time and with fewer hassles. Check out the blog post [here](https://bit.ly/3etiRyQ).

---

### Continuous Access Evaluation

**Type:** New feature

**Service category:** Authentications (Logins)

**Product capability:** Identity Security & Protection

Continuous Access Evaluation is a new security feature that enables near real-time enforcement of policies on relying parties consuming Azure AD Access Tokens when events happen in Azure AD (such as user account deletion). We are rolling this feature out first for Teams and Outlook clients. For more details, please read our [blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) and  [documentation](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### SMS Sign-in: Firstline Workers can sign in to Azure AD-backed applications with their phone number and no password

**Type:** New feature

**Service category:** Authentications (Logins)

**Product capability:** User Authentication

Office is launching a series of mobile-first business apps that cater to non-traditional organizations, and to employees in large organizations that don’t use email as their primary communication method. These apps target frontline employees, deskless workers, field agents, or retail employees that may not get an email address from their employer, have access to a computer, or to IT. This project will let these employees sign in to business applications by entering a phone number and roundtripping a code. For more details, please see our [admin documentation](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) and [end user documentation](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### Invite internal users to use B2B collaboration

**Type:** New feature

**Service category:** B2B

**Product capability:**

We're expanding B2B invitation capability to allow existing internal accounts to be invited to use B2B collaboration credentials going forward. This is done by passing the user object to the Invite API in addition to typical parameters like the invited email address. The user's object ID, UPN, group membership, app assignment, etc. remain intact, but going forward they'll use B2B to authenticate with their home tenant credentials rather than the internal credentials they used before the invitation. For details, see the [documentation](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### Report-only mode for Conditional Access is now generally available

**Type:** New feature

**Service category:** Conditional Access

**Product capability:** Identity Security & Protection

[Report-only mode for Azure AD Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) lets you evaluate the result of a policy without enforcing access controls. You can test report-only policies across your organization and understand their impact before enabling them, making deployment safer and easier. Over the past few months, we’ve seen strong adoption of report-only mode, with over 26M users already in scope of a report-only policy. With this announcement, new Azure AD Conditional Access policies will be created in report-only mode by default. This means you can monitor the impact of your policies from the moment they’re created. And for those of you who use the MS Graph APIs, you can also [manage report-only policies programmatically](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### Conditional Access insights and reporting workbook is generally available

**Type:** New feature

**Service category:** Conditional Access

**Product capability:** Identity Security & Protection

The Conditional Access [insights and reporting workbook](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) gives admins a summary view of Azure AD Conditional Access in their tenant. With the capability to select an individual policy, admins can better understand what each policy does and monitor any changes in real time. The workbook streams data stored in Azure Monitor, which you can set up in a few minutes [following these instructions](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). To make the dashboard more discoverable, we’ve moved it to the new insights and reporting tab within the Azure AD Conditional Access menu.

---

### Policy details blade for Conditional Access is in public preview

**Type:** New feature

**Service category:** Conditional Access

**Product capability:** Identity Security & Protection

The new [policy details blade](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) displays which assignments, conditions, and controls were satisfied during conditional access policy evaluation. You can access the blade by selecting a row in the **Conditional Access** or **Report-only** tabs of the Sign-in details.

---

### New Federated Apps available in Azure AD App gallery - April 2020

**Type:** New feature

**Service category:** Enterprise Apps

**Product capability:** 3rd Party Integration

In April 2020, we've added these 31 new apps with Federation support to the app gallery: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO for Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 Mover](https://app.mover.io/login), [Speaker Engage](https://speakerengage.com/login.php), [Honestly](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [empower](https://www.made-in-office.com/en/), [Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [MongoDB Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

For more information about the apps, see [SaaS application integration with Azure Active Directory](https://aka.ms/appstutorial). For more information about listing your application in the Azure AD app gallery, see [List your application in the Azure Active Directory application gallery](https://aka.ms/azureadapprequest).

---

### Microsoft Graph delta query support for oAuth2PermissionGrant available for Public Preview

**Type:** New feature

**Service category:** MS Graph

**Product capability:** Developer Experience

Delta query for oAuth2PermissionGrant is available for public preview! You can now track changes without having to continuously poll Microsoft Graph. [Learn more.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### Microsoft Graph delta query support for organizational contact generally available

**Type:** New feature

**Service category:** MS Graph

**Product capability:** Developer Experience

Delta query for organizational contacts is generally available! You can now track changes in production apps without having to continuously poll Microsoft Graph. Replace any existing code that continuously polls orgContact data by delta query to significantly improve performance. [Learn more.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### Microsoft Graph delta query support for application generally available

**Type:** New feature

**Service category:** MS Graph

**Product capability:** Developer Experience

Delta query for applications is generally available! You can now track changes in production apps without having to continuously poll Microsoft Graph. Replace any existing code that continuously polls application data by delta query to significantly improve performance. [Learn more.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### Microsoft Graph delta query support for administrative units available for Public Preview

**Type:** New feature

**Service category:** MS Graph

**Product capability:** Developer Experience
Delta query for administrative units is available for public preview! You can now track changes without having to continuously poll Microsoft Graph. [Learn more.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### Manage authentication phone numbers and more in new Microsoft Graph beta APIs

**Type:** New feature

**Service category:** MS Graph

**Product capability:** Developer Experience

These APIs are a key tool for managing your users’ authentication methods. Now you can programmatically pre-register and manage the authenticators used for MFA and self-service password reset (SSPR). This has been one of the most-requested features in the Azure MFA, SSPR, and Microsoft Graph spaces. The new APIs we’ve released in this wave give you the ability to:

- Read, add, update, and remove a user’s authentication phones
- Reset a user’s password
- Turn on and off SMS-sign-in

For more information, see [Azure AD authentication methods API overview](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### Administrative Units Public Preview

**Type:** New feature

**Service category:** RBAC

**Product capability:** Access Control

Administrative units allow you to grant admin permissions that are restricted to a department, region, or other segment of your organization that you define. You can use administrative units to delegate permissions to regional administrators or to set policy at a granular level. For example, a User account admin could update profile information, reset passwords, and assign licenses for users only in their administrative unit.

Using administrative units, a central administrator could:

- Create an administrative unit for decentralized management of resources
- Assign a role with administrative permissions over only Azure AD users in an administrative unit
- Populate the administrative units with users and groups as needed

For more information, see [Administrative units management in Azure Active Directory (preview)](https://aka.ms/AdminUnitsDocs).

---

### Printer Administrator and Printer Technician built-in roles

**Type:** New feature

**Service category:** RBAC

**Product capability:** Access Control

**Printer Administrator**: Users with this role can register printers and manage all aspects of all printer configurations in the Microsoft Universal Print solution, including the Universal Print Connector settings. They can consent to all delegated print permission requests. Printer Administrators also have access to print reports. 

**Printer Technician**: Users with this role can register printers and manage printer status in the Microsoft Universal Print solution. They can also read all connector information. Key tasks a Printer Technician cannot do are set user permissions on printers and sharing printers. [Learn more.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### Hybrid Identity Admin built-in role

**Type:** New feature

**Service category:** RBAC

**Product capability:** Access Control

Users in this role can enable, configure and manage services and settings related to enabling hybrid identity in Azure AD. This role grants the ability to configure Azure AD to one of the three supported authentication methods&#8212;Password hash synchronization (PHS), Pass-through authentication (PTA) or Federation (AD FS or 3rd party federation provider)&#8212;and to deploy related on-premises infrastructure to enable them. On-premises infrastructure includes Provisioning and PTA agents. This role grants the ability to enable Seamless Single Sign-On (S-SSO) to enable seamless authentication on non-Windows 10 devices or non-Windows Server 2016 computers. In addition, this role grants the ability to see sign-in logs and to access health and analytics for monitoring and troubleshooting purposes. [Learn more.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### Network Administrator built-in role

**Type:** New feature

**Service category:** RBAC

**Product capability:** Access Control

Users with this role can review network perimeter architecture recommendations from Microsoft that are based on network telemetry from their user locations. Network performance for Office 365 relies on careful enterprise customer network perimeter architecture, which is generally user location-specific. This role allows for editing of discovered user locations and configuration of network parameters for those locations to facilitate improved telemetry measurements and design recommendations. [Learn more.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### Bulk activity and downloads in the Azure AD admin portal experience

**Type:** New feature

**Service category:** User Management

**Product capability:** Directory

Now you can perform bulk activities on users and groups in Azure AD by uploading a CSV file in the Azure AD admin portal experience. You can create users, delete users, and invite guest users. And you can add and remove members from a group.

You can also download lists of Azure AD resources from the Azure AD admin portal experience. You can download the list of users in the directory, the list of groups in the directory, and the members of a particular group.

For more information, check out the following:

- [Create users](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) or [invite guest users](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Delete users](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) or [restore deleted users](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Download list of users](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) or [Download list of groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Add (import) members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) or [remove members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) or [Download list of members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) for a group

---

### My Staff delegated user management

**Type:** New feature

**Service category:** User Management

**Product capability:**

My Staff enables Firstline Managers, such as a store manager, to ensure that their staff members are able to access their Azure AD accounts. Instead of relying on a central helpdesk, organizations can delegate common tasks, such as resetting passwords or changing phone numbers, to a Firstline Manager. With My Staff, a user who can’t access their account can re-gain access in just a couple of clicks, with no helpdesk or IT staff required. For more information, see the [Manage your users with My Staff (preview)](https://aka.ms/MyStaffAdminDocs) and [Delegate user management with My Staff (preview)](https://aka.ms/MyStaffUserDocs).

---

### An upgraded end user experience in access reviews

**Type:** Changed feature

**Service category:** Access Reviews

**Product capability:** Identity Governance

We have updated the reviewer experience for Azure AD access reviews in the My Apps portal. At the end of April, your reviewers who are logged in to the Azure AD access reviews reviewer experience will see a banner that will allow them to try the updated experience in My Access. Please note that the updated Access reviews experience offers the same functionality as the current experience, but with an improved user interface on top of new capabilities to enable your users to be productive. [You can learn more about the updated experience here](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). This public preview will last until the end of July 2020. At the end of July, reviewers who have not opted into the preview experience will be automatically directed to My Access to perform access reviews. If you wish to have your reviewers permanently switched over to the preview experience in My Access now, [please make a request here](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### Workday inbound user provisioning and writeback apps now support the latest versions of Workday Web Services API

**Type:** Changed feature

**Service category:** App Provisioning

**Product capability:** 

Based on customer feedback, we have now updated the Workday inbound user provisioning and writeback apps in the enterprise app gallery to support the latest versions of the Workday Web Services (WWS) API. With this change, customers can specify the WWS API version that they would like to use in the connection string. This gives customers the ability to retrieve more HR attributes available in the releases of Workday. The Workday Writeback app now uses the recommended Change_Work_Contact_Info Workday web service to overcome the limitations of Maintain_Contact_Info.

If no version is specified in the connection string, by default, the Workday inbound provisioning apps will continue to use WWS v21.1  To switch to the latest Workday APIs for inbound user provisioning, customers need to update the connection string as documented [in the tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) and also update the XPATHs used for Workday attributes as documented in the [Workday attribute reference guide](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

To use the new API for writeback, there are no changes required in the Workday Writeback provisioning app. On the Workday side, ensure that the Workday Integration System User (ISU) account has permissions to invoke the Change_Work_Contact business process as documented in the tutorial section, [Configure business process security policy permissions](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

We have updated our [tutorial guide](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) to reflect the new API version support.

---

### Users with default access role are now in scope for provisioning

**Type:** Changed feature

**Service category:** App Provisioning

**Product capability:** Identity Lifecycle Management

Historically, users with the default access role have been out of scope for provisioning. We've heard feedback that customers want users with this role to be in scope for provisioning. As of April 16, 2020, all new provisioning configurations allow users with the default access role to be provisioned. Gradually we will change the behavior for existing provisioning configurations to support provisioning users with this role. [Learn more.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### Updated provisioning UI

**Type:** Changed feature

**Service category:** App Provisioning

**Product capability:** Identity Lifecycle Management

We've refreshed our provisioning experience to create a more focused management view. When you navigate to the provisioning blade for an enterprise application that has already been configured, you'll be able to easily monitor the progress of provisioning and manage actions such as starting, stopping, and restarting provisioning. [Learn more.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### Dynamic Group rule validation is now available for Public Preview

**Type:** Changed feature

**Service category:** Group Management

**Product capability:** Collaboration

Azure Active Directory (Azure AD) now provides the means to validate dynamic group rules. On the **Validate rules** tab, you can validate your dynamic rule against sample group members to confirm the rule is working as expected. When creating or updating dynamic group rules, administrators want to know whether a user or a device will be a member of the group. This helps evaluate whether a user or device meets the rule criteria and aids in troubleshooting when membership is not expected.

For more information, see [Validate a dynamic group membership rule (preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### Identity Secure Score - Security Defaults and MFA improvement action updates

**Type:** Changed feature

**Service category:** N/A

**Product capability:** Identity Security & Protection

**Supporting security defaults for Azure AD improvement actions:** Microsoft Secure Score will be updating improvement actions to support [security defaults in Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), which make it easier to help protect your organization with pre-configured security settings for common attacks. This will affect the following improvement actions:

- Ensure all users can complete multi-factor authentication for secure access
- Require MFA for administrative roles
- Enable policy to block legacy authentication
 
**MFA improvement action updates:** To reflect the need for businesses to ensure the upmost security while applying policies that work with their business, Microsoft Secure Score has removed three improvement actions centered around multi-factor authentication and added two.

Removed improvement actions:

- Register all users for multi-factor authentication
- Require MFA for all users
- Require MFA for Azure AD privileged roles

Added improvement actions:

- Ensure all users can complete multi-factor authentication for secure access
- Require MFA for administrative roles

These new improvement actions require registering your users or admins for multi-factor authentication (MFA) across your directory and establishing the right set of policies that fit your organizational needs. The main goal is to have flexibility while ensuring all your users and admins can authenticate with multiple factors or risk-based identity verification prompts. That can take the form of having multiple policies that apply scoped decisions, or setting security defaults (as of March 16th) that let Microsoft decide when to challenge users for MFA. [Read more about what's new in Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## March 2020

### Unmanaged Azure Active Directory accounts in B2B update for March, 2021

**Type:** Plan for change  
**Service category:** B2B  
**Product capability:** B2B/B2C
 
**Beginning on March 31, 2021**, Microsoft will no longer support the redemption of invitations by creating unmanaged Azure Active Directory (Azure AD) accounts and tenants for B2B collaboration scenarios. In preparation for this, we encourage you to opt in to [email one-time passcode authentication](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### Users with the default access role will be in scope for provisioning

**Type:** Plan for change  
**Service category:** App Provisioning  
**Product capability:** Identity Lifecycle Management
 
Historically, users with the default access role have been out of scope for provisioning. We've heard feedback that customers want users with this role to be in scope for provisioning. We're working on deploying a change so that all new provisioning configurations will allow users with the default access role to be provisioned. Gradually, we'll change the behavior for existing provisioning configurations to support provisioning users with this role. No customer action is required. We'll post an update to our [documentation](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) once this change is in place.

---

### Azure AD B2B collaboration will be available in Microsoft Azure operated by 21Vianet (Azure China 21Vianet) tenants

**Type:** Plan for change  
**Service category:** B2B  
**Product capability:** B2B/B2C
 
The Azure AD B2B collaboration capabilities will be made available in Microsoft Azure operated by 21Vianet (Azure China 21Vianet) tenants, enabling users in an Azure China 21Vianet tenant to collaborate seamlessly with users in other Azure China 21Vianet tenants. [Learn more about Azure AD B2B collaboration](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### Azure AD B2B Collaboration invitation email redesign

**Type:** Plan for change  
**Service category:** B2B  
**Product capability:** B2B/B2C
 
The [emails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) that are sent by the Azure AD B2B collaboration invitation service to invite users to the directory will be redesigned to make the invitation information and the user's next steps clearer.

---

### HomeRealmDiscovery policy changes will appear in the audit logs

**Type:** Fixed  
**Service category:** Audit  
**Product capability:** Monitoring & Reporting
 
We fixed a bug where changes to the [HomeRealmDiscovery policy](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) were not included in the audit logs. You will now be able to see when and how the policy was changed, and by whom. 

---

### New Federated Apps available in Azure AD App gallery - March 2020

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration
 
In March 2020, we've added these 51 new apps with Federation support to the app gallery: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai SPHERE](https://contexxt-sphere.com/login), [Wisdom By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), [Logz.io - Cloud Observability for Engineers](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [RIB A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx), [IP Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight Compliant Access Management](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [sybo](https://www.systexsoftware.com.tw/), [Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

For more information about the apps, see [SaaS application integration with Azure Active Directory](https://aka.ms/appstutorial). For more information about listing your application in the Azure AD app gallery, see [List your application in the Azure Active Directory application gallery](https://aka.ms/azureadapprequest).

---

### Azure AD B2B Collaboration available in Azure Government tenants

**Type:** New feature  
**Service category:** B2B  
**Product capability:** B2B/B2C
 
The Azure AD B2B collaboration features are now available between some Azure Government tenants.  To find out if your tenant is able to use these capabilities, follow the instructions at [How can I tell if B2B collaboration is available in my Azure US Government tenant?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### Azure Monitor integration for Azure Logs is now available in Azure Government

**Type:** New feature  
**Service category:** Reporting  
**Product capability:** Monitoring & Reporting
 
Azure Monitor integration with Azure AD logs is now available in Azure Government. You can route Azure AD Logs (Audit and Sign-in Logs) to a storage account, Event Hub and Log Analytics. Please check out the [detailed documentation](https://aka.ms/aadlogsinamd) as well as [deployment plans for reporting and monitoring](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) for Azure AD scenarios.

---

### Identity Protection Refresh in Azure Government

**Type:** New feature  
**Service category:** Identity Protection  
**Product capability:** Identity Security & Protection

We’re excited to share that we have now rolled out the refreshed [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) experience in the [Microsoft Azure Government portal](https://portal.azure.us/). For more information, see our [announcement blog post](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### Disaster recovery: Download and store your provisioning configuration

**Type:** New feature  
**Service category:** App Provisioning  
**Product capability:** Identity Lifecycle Management
 
The Azure AD provisioning service provides a rich set of configuration capabilities. Customers need to be able to save their configuration so that they can refer to it later or roll back to a known good version. We've added the ability to download your provisioning configuration as a JSON file and upload it when you need it. [Learn more](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### SSPR (self-service password reset) now requires two gates for admins in Microsoft Azure operated by 21Vianet (Azure China 21Vianet) 

**Type:** Changed feature  
**Service category:** Self-Service Password Reset  
**Product capability:** Identity Security & Protection
 
Previously in Microsoft Azure operated by 21Vianet (Azure China 21Vianet), admins using self-service password reset (SSPR) to reset their own passwords needed only one "gate" (challenge) to prove their identity. In public and other national clouds, admins generally must use two gates to prove their identity when using SSPR. But because we didn't support SMS or phone calls in Azure China 21Vianet, we allowed one-gate password reset by admins.

We're creating SSPR feature parity between Azure China 21Vianet and the public cloud. Going forward, admins must use two gates when using SSPR. SMS, phone calls, and Authenticator app notifications and codes will be supported. [Learn more](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### Password length is limited to 256 characters

**Type:** Changed feature  
**Service category:** Authentications (Logins)  
**Product capability:** User Authentication
 
To ensure the reliability of the Azure AD service, user passwords are now limited in length to 256 characters. Users with passwords longer than this will be asked to change their password on subsequent login, either by contacting their admin or by using the self-service password reset feature.

This change was enabled on March 13th, 2020, at 10AM PST (18:00 UTC), and the error is AADSTS 50052, InvalidPasswordExceedsMaxLength. See the [breaking change notice](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) for more details.

---

### Azure AD sign-in logs are now available for all free tenants through the Azure portal

**Type:** Changed feature  
**Service category:** Reporting  
**Product capability:** Monitoring & Reporting
 
Starting now, customers who have free tenants can access the [Azure AD sign-in logs from the Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) for up to 7 days. Previously, sign-in logs were available only for customers with Azure Active Directory Premium licenses. With this change, all tenants can access these logs through the portal.

> [!NOTE]
> Customers still need a premium license (Azure Active Directory Premium P1 or P2) to access the sign-in logs through Microsoft Graph API and Azure Monitor.

---

### Deprecation of Directory-wide groups option from Groups General Settings on Azure portal

**Type:** Deprecated  
**Service category:** Group Management  
**Product capability:** Collaboration

To provide a more flexible way for customers to create directory-wide groups that best meet their needs, we've replaced the **Directory-wide Groups** option from the **Groups** > **General** settings in the Azure portal with a link to [dynamic group documentation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). We've improved our documentation to include more instructions so administrators can create all-user groups that include or exclude guest users.

---

## February 2020

### Upcoming changes to custom controls

**Type:** Plan for change  
**Service category:** MFA  
**Product capability:** Identity Security & Protection
 
We're planning to replace the current custom controls preview with an approach that allows partner-provided authentication capabilities to work seamlessly with the Azure Active Directory administrator and end user experiences. Today, partner MFA solutions face the following limitations: they work only after a password has been entered; they don't serve as MFA for step-up authentication in other key scenarios; and they don't integrate with end user or administrative credential management functions. The new implementation will allow partner-provided authentication factors to work alongside built-in factors for key scenarios, including registration, usage, MFA claims, step up authentication, reporting, and logging. 

Custom controls will continue to be supported in preview alongside the new design until it reaches general availability. At that point, we'll give customers time to migrate to the new design. Because of the limitations of the current approach, we won't onboard new providers until the new design is available. We are working closely with customers and providers and will communicate the timeline as we get closer. [Learn more](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### Identity Secure Score - MFA improvement action updates

**Type:** Plan for change  
**Service category:** MFA  
**Product capability:** Identity Security & Protection
 
To reflect the need for businesses to ensure the upmost security while applying policies that work with their business, Microsoft Secure Score is removing three improvement actions centered around multi-factor authentication (MFA), and adding two.

The following improvement actions will be removed:

- Register all users for MFA
- Require MFA for all users
- Require MFA for Azure AD privileged roles

The following improvement actions will be added:

- Ensure all users can complete MFA for secure access
- Require MFA for administrative roles

These new improvement actions will require registering your users or admins for MFA across your directory and establishing the right set of policies that fit your organizational needs. The main goal is to have flexibility while ensuring all your users and admins can authenticate with multiple factors or risk-based identity verification prompts. This can take the form of setting security defaults that let Microsoft decide when to challenge users for MFA, or having multiple policies that apply scoped decisions. As part of these improvement action updates, Baseline protection policies will no longer be included in scoring calculations. [Read more about what's coming in Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### Azure AD Domain Services SKU selection

**Type:** New feature  
**Service category:** Azure AD Domain Services  
**Product capability:** Azure AD Domain Services
 
We've heard feedback that Azure AD Domain Services customers want more flexibility in selecting performance levels for their instances. Starting on February 1, 2020, we switched from a dynamic model (where Azure AD determines the performance and pricing tier based on object count) to a self-selection model. Now customers can choose a performance tier that matches their environment. This change also allows us to enable new scenarios like Resource Forests, and Premium features like daily backups. The object count is now unlimited for all SKUs, but we'll continue to offer object count suggestions for each tier.

**No immediate customer action is required.** For existing customers, the dynamic tier that was in use on February 1, 2020, determines the new default tier. There is no pricing or performance impact as the result of this change. Going forward, Azure AD DS customers will need to evaluate performance requirements as their directory size and workload characteristics change. Switching between service tiers will continue to be a no-downtime operation, and we will no longer automatically move customers to new tiers based on the growth of their directory. Furthermore, there will be no price increases, and new pricing will align with our current billing model. For more information, see the [Azure AD DS SKUs documentation](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) and the [Azure AD Domain Services pricing page](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### New Federated Apps available in Azure AD App gallery - February 2020

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration
 
In February 2020, we've added these 31 new apps with Federation support to the app gallery: 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial),
 [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial),
 [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial),
 [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure),
 [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad),
 [In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial),
 [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial),
 [Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial),
 [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess),
 [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial),
 [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial),
 [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial),
 [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial),
 [New Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial),
 [Thulium](https://admin.thulium.com/login/instance),
 [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial),
 [Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams),
 [Beesy](https://www.beesy.me/index.php/site/login),
 [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial),
 [MURAL](https://app.mural.co/signup),
 [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial),
 [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview),
 [Wakelet](https://wakelet.com/login),
 [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial),
 [ThingLink for Teachers and Schools](https://www.thinglink.com/),
 [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial),
 [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product),
 [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial),
 [InvitePeople](https://invitepeople.com/login),
 [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial),
 [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
For more information about the apps, see [SaaS application integration with Azure Active Directory](https://aka.ms/appstutorial). For more information about listing your application in the Azure AD app gallery, see [List your application in the Azure Active Directory application gallery](https://aka.ms/azureadapprequest).

---
 
### New provisioning connectors in the Azure AD Application Gallery - February 2020

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration
 
You can now automate creating, updating, and deleting user accounts for these newly integrated apps:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

For more information about how to better secure your organization by using automated user account provisioning, see [Automate user provisioning to SaaS applications with Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### Azure AD support for FIDO2 security keys in hybrid environments

**Type:** New feature  
**Service category:** Authentications (Logins)  
**Product capability:** User Authentication
 
We're announcing the public preview of Azure AD support for FIDO2 security keys in Hybrid environments. Users can now use FIDO2 security keys to sign in to their Hybrid Azure AD joined Windows 10 devices and get seamless sign-on to their on-premises and cloud resources. Support for Hybrid environments has been the top most-requested feature from our passwordless customers since we initially launched the public preview for FIDO2 support in Azure AD joined devices. Passwordless authentication using advanced technologies like biometrics and public/private key cryptography provide convenience and ease-of-use while being secure. With this public preview, you can now use modern authentication like FIDO2 security keys to access traditional Active Directory resources. For more information, go to [SSO to on-premises resources](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

To get started, visit [enable FIDO2 security keys for your tenant](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) for step-by-step instructions. 

---
 
### The new My Account experience is now generally available

**Type:** Changed feature  
**Service category:** My Profile/Account  
**Product capability:** End User Experiences
 
My Account, the one stop shop for all end-user account management needs, is now generally available! End users can access this new site via URL, or in the header of the new My Apps experience. Learn more about all the self-service capabilities the new experience offers at [My Account Portal Overview](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### My Account site URL updating to myaccount.microsoft.com

**Type:** Changed feature  
**Service category:** My Profile/Account  
**Product capability:** End User Experiences
 
The new My Account end user experience will be updating its URL to `https://myaccount.microsoft.com` in the next month. Find more information about the experience and all the account self-service capabilities it offers to end users at [My Account portal help](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## January 2020
 
### The new My Apps portal is now generally available

**Type:** Plan for change  
**Service category:** My Apps  
**Product capability:** End User Experiences
 
Upgrade your organization to the new My Apps portal that is now generally available! Find more information on the new portal and collections at [Create collections on the My Apps portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### Workspaces in Azure AD have been renamed to collections

**Type:** Changed feature  
**Service category:** My Apps   
**Product capability:** End User Experiences
 
Workspaces, the filters admins can configure to organize their users' apps, will now be referred to as collections. Find more info on how to configure them at [Create collections on the My Apps portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### Azure AD B2C Phone sign-up and sign-in using custom policy (Public Preview)

**Type:** New feature  
**Service category:** B2C - Consumer Identity Management  
**Product capability:** B2B/B2C
 
With phone number sign-up and sign-in, developers and enterprises can allow their customers to sign up and sign in using a one-time password sent to the user's phone number via SMS. This feature also lets the customer change their phone number if they lose access to their phone. With the power of custom policies, phone sign-up and sign-in allows developers and enterprises to communicate their brand through page customization. Find out how to [set up phone sign-up and sign-in with custom policies in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### New provisioning connectors in the Azure AD Application Gallery - January 2020

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration
 
You can now automate creating, updating, and deleting user accounts for these newly integrated apps:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

For more information about how to better secure your organization by using automated user account provisioning, see [Automate user provisioning to SaaS applications with Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### New Federated Apps available in Azure AD App gallery - January 2020

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration
 
In January 2020, we've added these 33 new apps with Federation support to the app gallery: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft, and JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [ PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

For more information about the apps, see [SaaS application integration with Azure Active Directory](https://aka.ms/appstutorial). For more information about listing your application in the Azure AD app gallery, see [List your application in the Azure Active Directory application gallery](https://aka.ms/azureadapprequest).

---

### Two new Identity Protection detections

**Type:** New feature  
**Service category:** Identity Protection  
**Product capability:** Identity Security & Protection
 
We've added two new sign-in linked detection types to Identity Protection: Suspicious inbox manipulation rules and Impossible travel. These offline detections are discovered by Microsoft Cloud App Security (MCAS) and influence the user and sign-in risk in Identity Protection. For more information on these detections, see our [sign-in risk types](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### Breaking Change: URI Fragments will not be carried through the login redirect

**Type:** Changed feature  
**Service category:** Authentications (Logins)  
**Product capability:** User Authentication
 
Starting on February 8, 2020, when a request is sent to login.microsoftonline.com to sign in a user, the service will append an empty fragment to the request.  This prevents a class of redirect attacks by ensuring that the browser wipes out any existing fragment in the request. No application should have a dependency on this behavior. For more information, see [Breaking changes](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) in the Microsoft identity platform documentation.

---

## December 2019

### Integrate SAP SuccessFactors provisioning into Azure AD and on-premises AD (Public Preview)

**Type:** New feature  
**Service category:** App Provisioning  
**Product capability:** Identity Lifecycle Management

You can now integrate SAP SuccessFactors as an authoritative identity source in Azure AD. This integration helps you automate the end-to-end identity lifecycle, including using HR-based events, like new hires or terminations, to control provisioning of Azure AD accounts.

For more information about how to set up SAP SuccessFactors inbound provisioning to Azure AD, see the [Configure SAP SuccessFactors automatic provisioning](https://aka.ms/SAPSuccessFactorsInboundTutorial) tutorial.

---

### Support for customized emails in Azure AD B2C (Public Preview)

**Type:** New feature  
**Service category:** B2C - Consumer Identity Management  
**Product capability:** B2B/B2C

You can now use Azure AD B2C to create customized emails when your users sign up to use your apps. By using DisplayControls (currently in preview) and a third-party email provider (such as, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/), or a custom REST API), you can use your own email template, **From** address, and subject text, as well as support localization and custom one-time password (OTP) settings.

For more information, see [Custom email verification in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### Replacement of baseline policies with security defaults

**Type:** Changed feature  
**Service category:** Other  
**Product capability:** Identity Security and Protection

As part of a secure-by-default model for authentication, we're removing the existing baseline protection policies from all tenants. This removal is targeted for completion at the end of February. The replacement for these baseline protection policies is security defaults. If you've been using baseline protection policies, you must plan to move to the new security defaults policy or to Conditional Access. If you haven't used these policies, there is no action for you to take.

For more information about the new security defaults, see [What are security defaults?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) For more information about Conditional Access policies, see [Common Conditional Access policies](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## November 2019

### Support for the SameSite attribute and Chrome 80

**Type:** Plan for change  
**Service category:** Authentications (Logins)  
**Product capability:** User Authentication

As part of a secure-by-default model for cookies, the Chrome 80 browser is changing how it treats cookies without the `SameSite` attribute. Any cookie that doesn't specify the `SameSite` attribute will be treated as though it was set to `SameSite=Lax`, which will result in Chrome blocking certain cross-domain cookie sharing scenarios that your app may depend on. To maintain the older Chrome behavior, you can use the `SameSite=None` attribute and add an additional `Secure` attribute, so cross-site cookies can only be accessed over HTTPS connections. Chrome is scheduled to complete this change by February 4, 2020.

We recommend all our developers test their apps using this guidance:

- Set the default value for the **Use Secure Cookie** setting to **Yes**.

- Set the default value for the **SameSite** attribute to **None**.

- Add an additional `SameSite` attribute of **Secure**.

For more information, see [Upcoming SameSite Cookie Changes in ASP.NET and ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) and [Potential disruption to customer websites and Microsoft products and services in Chrome version 79 and later](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### New hotfix for Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Type:** Fixed  
**Service category:** Microsoft Identity Manager  
**Product capability:** Identity Lifecycle Management

A hotfix rollup package (build 4.6.34.0) is available for Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). This rollup package resolves issues and adds improvements that are described in the "Issues fixed and improvements added in this update" section.

For more information and to download the hotfix package, see [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup is available](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### New AD FS app activity report to help migrate apps to Azure AD (Public Preview)

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** SSO

Use the new Active Directory Federation Services (AD FS) app activity report, in the Azure portal, to identify which of your apps are capable of being migrated to Azure AD. The report assesses all AD FS apps for compatibility with Azure AD, checks for any issues, and gives guidance about preparing individual apps for migration.

For more information, see [Use the AD FS application activity report to migrate applications to Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### New workflow for users to request administrator consent (Public Preview)

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** Access Control

The new admin consent workflow gives admins a way to grant access to apps that require admin approval. If a user tries to access an app, but is unable to provide consent, they can now send a request for admin approval. The request is sent by email, and placed in a queue that's accessible from the Azure portal, to all the admins who have been designated as reviewers. After a reviewer takes action on a pending request, the requesting users are notified of the action.

For more information, see [Configure the admin consent workflow (preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### New Azure AD App Registrations Token configuration experience for managing optional claims (Public Preview)

**Type:** New feature  
**Service category:** Other  
**Product capability:** Developer Experience

The new **Azure AD App Registrations Token configuration** blade on the Azure portal now shows app developers a dynamic list of optional claims for their apps. This new experience helps to streamline Azure AD app migrations and to minimize optional claims misconfigurations.

For more information, see [Provide optional claims to your Azure AD app](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### New two-stage approval workflow in Azure AD entitlement management (Public Preview)

**Type:** New feature  
**Service category:** Other  
**Product capability:** Entitlement Management

We've introduced a new two-stage approval workflow that allows you to require two approvers to approve a user's request to an access package. For example, you can set it so the requesting user's manager must first approve, and then you can also require a resource owner to approve. If one of the approvers doesn't approve, access isn't granted.

For more information, see [Change request and approval settings for an access package in Azure AD entitlement management](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### Updates to the My Apps page along with new workspaces (Public Preview)

**Type:** New feature  
**Service category:** My Apps  
**Product capability:** 3rd Party Integration

You can now customize the way your organization's users view and access the refreshed My Apps experience. This new experience also includes the new workspaces feature, which makes it easier for your users to find and organize apps.

For more information about the new My Apps experience and creating workspaces, see [Create workspaces on the My Apps portal](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### Google social ID support for Azure AD B2B collaboration (General Availability)

**Type:** New feature  
**Service category:** B2B  
**Product capability:** User Authentication

New support for using Google social IDs (Gmail accounts) in Azure AD helps to make collaboration simpler for your users and partners. There's no longer a need for your partners to create and manage a new Microsoft-specific account. Microsoft Teams now fully supports Google users on all clients and across the common and tenant-related authentication endpoints.

For more information, see [Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### Microsoft Edge Mobile Support for Conditional Access and Single Sign-on (General Availability)

**Type:** New feature  
**Service category:** Conditional Access  
**Product capability:** Identity Security & Protection

Azure AD for Microsoft Edge on iOS and Android now supports Azure AD Single Sign-On and Conditional Access:

- **Microsoft Edge single sign-on (SSO):** Single sign-on is now available across native clients (such as Microsoft Outlook and Microsoft Edge) for all Azure AD -connected apps.

- **Microsoft Edge conditional access:** Through application-based conditional access policies, your users must use Microsoft Intune-protected browsers, such as Microsoft Edge.

For more information about conditional access and SSO with Microsoft Edge, see the [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blog post. For more information about how to set up your client apps using [app-based conditional access](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) or [device-based conditional access](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), see [Manage web access using a Microsoft Intune policy-protected browser](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### Azure AD entitlement management (General Availability)

**Type:** New feature  
**Service category:** Other  
**Product capability:** Entitlement Management

Azure AD entitlement management is a new identity governance feature, which helps organizations manage identity and access lifecycle at scale. This new feature helps by automating access request workflows, access assignments, reviews, and expiration across groups, apps, and SharePoint Online sites.

With Azure AD entitlement management, you can more efficiently manage access both for employees  and also for users outside your organization who need access to those resources.

For more information, see [What is Azure AD entitlement management?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### Automate user account provisioning for these newly supported SaaS apps

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration  

You can now automate creating, updating, and deleting user accounts for these newly integrated apps:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

For more information about how to better secure your organization by using automated user account provisioning, see [Automate user provisioning to SaaS applications with Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### New Federated Apps available in Azure AD App gallery - November 2019

**Type:** New feature  
**Service category:** Enterprise Apps  
**Product capability:** 3rd Party Integration

In November 2019, we've added these 21 new apps with Federation support to the app gallery:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

For more information about the apps, see [SaaS application integration with Azure Active Directory](https://aka.ms/appstutorial). For more information about listing your application in the Azure AD app gallery, see [List your application in the Azure Active Directory application gallery](https://aka.ms/azureadapprequest).

---

### New and improved Azure AD application gallery

**Type:** Changed feature  
**Service category:** Enterprise Apps  
**Product capability:** SSO

We've updated the Azure AD application gallery to make it easier for you to find pre-integrated apps that support provisioning, OpenID Connect, and SAML on your Azure Active Directory tenant.

For more information, see [Add an application to your Azure Active Directory tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### Increased app role definition length limit from 120 to 240 characters

**Type:** Changed feature  
**Service category:** Enterprise Apps  
**Product capability:** SSO

We've heard from customers that the length limit for the app role definition value in some apps and services is too short at 120 characters. In response, we've increased the maximum length of the role value definition to 240 characters.

For more information about using application-specific role definitions, see [Add app roles in your application and receive them in the token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---
