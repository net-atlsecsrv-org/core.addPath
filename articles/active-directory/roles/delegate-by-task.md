---
title: Delegate roles by admin task - Azure Active Directory | Microsoft Docs
description: Roles to delegate for identity tasks in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
#Customer intent: As an Azure AD administrator, I want to know which role has the least privilege for a given task to make my Azure AD organization more secure.
---

# Administrator roles by admin task in Azure Active Directory

In this article, you can find the information needed to restrict a user's administrator permissions by assigning least privileged roles in Azure Active Directory (Azure AD). You will find administrator tasks organized by feature area and the least privileged role required to perform each task, along with additional non-Global Administrator roles that can perform the task.

## Application proxy

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure application proxy app | Application administrator | 
Configure connector group properties | Application administrator | 
Create application registration when ability is disabled for all users | Application developer | Cloud Application administrator, Application Administrator
Create connector group | Application administrator | 
Delete connector group | Application administrator | 
Disable application proxy | Application administrator | 
Download connector service | Application administrator | 
Read all configuration | Application administrator | 

## External Identities/B2C

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Create Azure AD B2C directories | All non-guest users ([see documentation](../fundamentals/users-default-permissions.md)) | 
Create B2C applications | Global Administrator | 
Create enterprise applications | Cloud Application Administrator | Application Administrator
Create, read, update, and delete B2C policies | B2C IEF Policy Administrator | 
Create, read, update, and delete identity providers | External Identity Provider Administrator | 
Create, read, update, and delete password reset user flows | External ID User Flow Administrator | 
Create, read, update, and delete profile editing user flows | External ID User Flow Administrator | 
Create, read, update, and delete sign-in user flows | External ID User Flow Administrator | 
Create, read, update, and delete sign-up user flow |External ID User Flow Administrator | 
Create, read, update, and delete user attributes | External ID User Flow Attribute Administrator | 
Create, read, update, and delete users | User Administrator
Read all configuration | Global reader | 
Read B2C audit logs | Global reader ([see documentation](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C Global administrators do not have the same permissions as Azure AD global administrators. If you have Azure AD B2C global administrator privileges, make sure that you are in an Azure AD B2C directory and not an Azure AD directory.

## Company branding

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure company branding | Global Administrator | 
Read all configuration | Directory readers | Default user role ([see documentation](../fundamentals/users-default-permissions.md))

## Company properties

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure company properties | Global Administrator | 

## Connect

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Passthrough authentication | Global Administrator  | 
Read all configuration | Global reader | Global Administrator  |
Seamless single sign-on | Global Administrator  | 

## Cloud Provisioning

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Passthrough authentication | Hybrid Identity Administrator  | 
Read all configuration | Global reader | Hybrid Identity Administrator  |
Seamless single sign-on | Hybrid Identity Administrator  | 

## Connect Health

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Add or delete services | Owner ([see documentation](../hybrid/how-to-connect-health-operations.md)) | 
Apply fixes to sync error | Contributor ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Owner
Configure notifications | Contributor ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Owner
Configure settings | Owner ([see documentation](../hybrid/how-to-connect-health-operations.md)) | 
Configure sync notifications | Contributor ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Owner
Read ADFS security reports | Security Reader | Contributor, Owner
Read all configuration | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner
Read sync errors | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner
Read sync services | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner
View metrics and alerts | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner
View metrics and alerts | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner
View sync service metrics and alerts | Reader ([see documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributor, Owner

## Custom domain names

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Manage domains | Domain Name Administrator | 
Read all configuration | Directory readers | Default user role ([see documentation](../fundamentals/users-default-permissions.md))

## Domain Services

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Create Azure AD Domain Services instance | Global Administrator | 
Perform all Azure AD Domain Services tasks | Azure AD DC??Administrators group ([see documentation](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Read all configuration | Reader on Azure subscription containing AD DS service | 

## Devices

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Disable device | Cloud device administrator | 
Enable device | Cloud device administrator | 
Read basic configuration | Default user role ([see documentation](../fundamentals/users-default-permissions.md)) | 
Read BitLocker keys | Security Reader | Password administrator, Security administrator

## Enterprise applications

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Consent to any delegated permissions | Cloud application administrator | Application administrator
Consent to application permissions not including Microsoft Graph | Cloud application administrator | Application administrator
Consent to application permissions to Microsoft Graph | Privileged Role Administrator | 
Consent to applications accessing own data | Default user role ([see documentation](../fundamentals/users-default-permissions.md)) | 
Create enterprise application | Cloud application administrator | Application administrator
Manage Application Proxy | Application administrator | 
Manage user settings | Global Administrator | 
Read access review of a group or of an app | Security Reader | Security Administrator, User Administrator
Read all configuration | Default user role ([see documentation](../fundamentals/users-default-permissions.md)) | 
Update enterprise application assignments | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator
Update enterprise application owners | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator
Update enterprise application properties | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator
Update enterprise application provisioning | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator
Update enterprise application self-service | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator
Update single sign-on properties | Enterprise application owner ([see documentation](../fundamentals/users-default-permissions.md)) | Cloud application administrator, Application administrator

## Entitlement management
Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Add resources to a catalog | User administrator | With entitlement management, you can delegate this task to the catalog owner ([see documentation](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Add SharePoint Online sites to catalog | Global administrator


## Groups

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Assign license | User administrator | 
Create group | Groups administrator | User administrator
Create, update, or delete access review of a group or of an app | User administrator | 
Manage group expiration | User administrator | 
Manage group settings | Groups Administrator | User Administrator | 
Read all configuration (except hidden membership) | Directory readers | Default user role ([see documentation](../fundamentals/users-default-permissions.md))
Read hidden membership | Group member | Group owner, Password administrator, Exchange administrator, SharePoint administrator, Teams administrator, User administrator
Read membership of groups with hidden membership | Helpdesk Administrator | User administrator, Teams administrator
Revoke license | License administrator | User administrator
Update group membership | Group owner ([see documentation](../fundamentals/users-default-permissions.md)) | User administrator
Update group owners | Group owner ([see documentation](../fundamentals/users-default-permissions.md)) | User administrator
Update group properties | Group owner ([see documentation](../fundamentals/users-default-permissions.md)) | User administrator
Delete group | Groups administrator | User administrator

## Identity Protection

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure alert notifications| Security Administrator | 
Configure and enable or disable MFA policy| Security Administrator | 
Configure and enable or disable sign-in risk policy| Security Administrator | 
Configure and enable or disable user risk policy | Security Administrator | 
Configure weekly digests | Security Administrator| 
Dismiss all risk detections | Security Administrator | 
Fix or dismiss vulnerability | Security Administrator | 
Read all configuration | Security Reader | 
Read all risk detections | Security Reader | 
Read vulnerabilities | Security Reader | 

## Licenses

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Assign license | License administrator | User administrator
Read all configuration | Directory readers | Default user role ([see documentation](../fundamentals/users-default-permissions.md))
Revoke license | License administrator | User administrator
Try or buy subscription | Billing administrator | 


## Monitoring - Audit logs

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Read audit logs | Reports reader | Security Reader, Security administrator

## Monitoring - Sign-ins

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Read sign-in logs | Reports reader | Security Reader, Security administrator

## Multi-factor authentication

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Delete all existing app passwords generated by the selected users | Global Administrator | 
Disable MFA | Authentication Administrator (via PowerShell) | Privileged Authentication Administrator (via PowerShell)
Enable MFA | Authentication Administrator (via PowerShell) | Privileged Authentication Administrator (via PowerShell) 
Manage MFA service settings | Authentication Policy Administrator | 
Require selected users to provide contact methods again | Authentication Administrator | 
Restore multi-factor authentication on all remembered devices?? | Authentication Administrator | 

## MFA Server

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Block/unblock users | Authentication Policy Administrator | 
Configure account lockout | Authentication Policy Administrator | 
Configure caching rules | Authentication Policy Administrator | 
Configure fraud alert | Authentication Policy Administrator
Configure notifications | Authentication Policy Administrator | 
Configure one-time bypass | Authentication Policy Administrator | 
Configure phone call settings | Authentication Policy Administrator | 
Configure providers | Authentication Policy Administrator | 
Configure server settings | Authentication Policy Administrator | 
Read activity report | Global reader | 
Read all configuration | Global reader | 
Read server status | Global reader |  

## Organizational relationships

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Manage identity providers | External Identity Provider Administrator | 
Manage settings | Global Administrator | 
Manage terms of use | Global Administrator | 
Read all configuration | Global reader | 

## Password reset

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure authentication methods | Global Administrator |
Configure customization | Global Administrator |
Configure notification | Global Administrator |
Configure on-premises integration | Global Administrator |
Configure password reset properties | User Administrator | Global Administrator
Configure registration | Global Administrator |
Read all configuration | Security Administrator | User Administrator |

## Privileged identity management

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Assign users to roles | Privileged role administrator | 
Configure role settings | Privileged role administrator | 
View audit activity | Security reader | 
View role memberships | Security reader | 

## Roles and administrators

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Manage role assignments | Privileged role administrator | 
Read access review of an Azure AD role  | Security Reader | Security administrator, Privileged role administrator
Read all configuration | Default user role ([see documentation](../fundamentals/users-default-permissions.md)) | 

## Security - Authentication methods

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure authentication methods | Global Administrator | 
Configure password protection | Security administrator
Configure smart lockout | Security administrator
Read all configuration | Global reader | 

## Security - Conditional Access

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Configure MFA trusted IP addresses | Conditional Access administrator | 
Create custom controls | Conditional Access administrator | Security administrator
Create named locations | Conditional Access administrator | Security administrator
Create policies | Conditional Access administrator | Security administrator
Create terms of use | Conditional Access administrator | Security administrator
Create VPN connectivity certificate | Conditional Access administrator | Security administrator
Delete classic policy | Conditional Access administrator | Security administrator
Delete terms of use | Conditional Access administrator | Security administrator
Delete VPN connectivity certificate | Conditional Access administrator | Security administrator
Disable classic policy | Conditional Access administrator | Security administrator
Manage custom controls | Conditional Access administrator | Security administrator
Manage named locations | Conditional Access administrator | Security administrator
Manage terms of use | Conditional Access administrator | Security administrator
Read all configuration | Security reader | Security administrator
Read named locations | Security reader | Conditional Access administrator, security administrator

## Security - Identity security score

Task | Least privileged role | Additional roles | 
---- | --------------------- | ----------------
Read all configuration | Security reader | Security administrator
Read security score | Security reader | Security administrator
Update event status | Security administrator | 

## Security - Risky sign-ins

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Read all configuration | Security Reader | 
Read risky sign-ins | Security Reader | 

## Security - Users flagged for risk

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Dismiss all events | Security Administrator | 
Read all configuration | Security Reader | 
Read users flagged for risk | Security Reader | 

## Users

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Add user to directory role | Privileged role administrator | 
Add user to group | User administrator | 
Assign license | License administrator | User administrator
Create guest user | Guest inviter | User administrator
Create user | User administrator | 
Delete users | User administrator | 
Invalidate refresh tokens of limited admins (see documentation) | User administrator | 
Invalidate refresh tokens of non-admins (see documentation) | Password administrator | User administrator
Invalidate refresh tokens of privileged admins (see documentation) | Privileged Authentication Administrator | 
Read basic configuration | Default User role ([see documentation](../fundamentals/users-default-permissions.md) | 
Reset password for limited admins (see documentation) | User administrator | 
Reset password of non-admins (see documentation) | Password administrator | User administrator
Reset password of privileged admins | Privileged Authentication Administrator | 
Revoke license | License administrator | User administrator
Update all properties except User Principal Name | User administrator | 
Update User Principal Name for limited admins (see documentation) | User administrator | 
Update User Principal Name property on privileged admins (see documentation) | Global Administrator | 
Update user settings | Global Administrator | 
Update Authentication methods | Authentication Administrator | Privileged Authentication Administrator, Global Administrator


## Support

Task | Least privileged role | Additional roles
---- | --------------------- | ----------------
Submit support ticket | Service Administrator | Application Administrator, Azure Information Protection Administrator, Billing Administrator, Cloud Application Administrator, Compliance Administrator, Dynamics 365 Administrator, Desktop Analytics Administrator, Exchange Administrator, Password Administrator, Intune Administrator, Skype for Business Administrator, Power BI Administrator, Privileged Authentication Administrator, SharePoint Administrator, Teams Communications Administrator, Teams Administrator, User Administrator, Workplace Analytics Administrator

## Next steps

* [How to assign or remove azure AD administrator roles](manage-roles-portal.md)
* [Azure AD administrator roles reference](permissions-reference.md)
