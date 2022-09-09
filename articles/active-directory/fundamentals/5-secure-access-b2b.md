---
title: Transition to governed collaboration with Azure Active Directory B2B Collaboration
description: Move to governed collaboration with Azure Ad B2B collaboration.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: "it-pro, seodec18"
ms.collection: M365-identity-device-management
---

# Transition to governed collaboration with Azure Active Directory B2B collaboration 

Getting your collaboration under control is key to securing external access to your resources. Before going forward with this article, be sure that you have:

* [Determined your security posture](1-secure-access-posture.md)

* [Discovered your current state](2-secure-access-current-state.md)

* [Created a security plan](3-secure-access-plan.md)

* [Understood how groups and security work together](4-secure-access-groups.md)

Once you’ve done those things, you're ready to move into controlled collaboration. This article will guide you to move all your external collaboration into [Azure Active Directory B2B collaboration](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure Ad B2B is a feature of [Azure AD External Identities](../external-identities/compare-with-b2c.md).

## Control who your organization collaborates with

You must decide whether to limit which organizations your users can collaborate with, and who within your organization can initiate collaboration. Most organizations take the approach of permitting business units to decide with whom they collaborate, and delegating the approval and oversight as needed. For example, some government, education, and financial services organizations don't permit open collaboration. You may wish to use the Azure AD features to scope collaboration, as discussed in the rest of this section.

### Determine collaboration partners

First, ensure you've documented the organizations you're currently collaborating with, and the domains for those organizations' users. One collaboration partner may have multiple domains. For example, a partner may have multiple business units with separate domains.

Next, determine if you want to enable future collaboration with 

* any domain (most inclusive)

* all domains except those explicitly denied 

* only specific domains (most restrictive)

> [!NOTE]
> The more restrictive your collaboration settings, the more likely that your users will go outside of your approved collaboration framework. We recommend enabling the broadest collaboration your security needs will allow, and closely reviewing that collaboration rather than being overly restrictive. 

Also note that limiting to a single domain may inadvertently prevent authorized collaboration with organizations, which have other unrelated domains for their users. For example, if doing business with an organization Contoso, the initial point of contact with Contoso might be one of their US-based employees who has an email with a ".com" domain. However if you only allow the ".com" domain you may inadvertently omit their Canadian employees who have ".ca" domain. 

There are circumstances in which you would want to only allow specific collaboration partners. For example, a university system may only want to allow their own faculty access to a resource tenant. Or a conglomerate may only want to allow specific subsidiaries to collaborate with each other to achieve compliance with a required framework.

#### Using allow and deny lists

You can use an allow list or deny list to [restrict invitations to B2B users](../external-identities/allow-deny-list.md) from specific organizations. You can use only an allow or a deny list, not both.

* An [allow list](../external-identities/allow-deny-list.md) limits collaboration to only those domains listed; all other domains are effectively on the deny list.

* A [deny list](../external-identities/allow-deny-list.md) allows collaboration with any domain not on the deny list.

> [!IMPORTANT]
> These lists do not apply to users who are already in your directory. They also do not apply to OneDrive for Business and SharePoint allow deny lists which are separate.

Some organizations use a list of known ‘bad actor’ domains provided by their managed security provider for their deny list. For example, if the organization is legitimately doing business with Contoso and using a .com domain, there may be an unrelated organization that has been using the Contoso .org domain and attempting a phishing attack to impersonate Contoso employees. 

## Control how external users gain access

There are many ways to collaborate with external partners using Azure AD B2B. To begin collaboration, you invite or otherwise enable your partner to access your resources. Users can gain access by responding to :

* Redeeming [an invitation sent via an email](../external-identities/redemption-experience.md), or [a direct link to share](../external-identities/redemption-experience.md) a resource.

* Requesting access [through an application](../external-identities/self-service-sign-up-overview.md) you create

* Requesting access through the [My Access](../governance/entitlement-management-request-access.md) portal

When you enable Azure AD B2B, you enable the ability to invite guest users via direct links and email invitations by default. Invitations via Email OTP and a self-service portal are currently in preview and must be enabled within the External Identities | External collaboration settings in the Azure AD portal.

### Control who can invite guest users

Determine who can invite guest users to access resources.

* The most restrictive setting is to allow only administrators and those users granted the [guest inviter role](../external-identities/delegate-invitations.md) to invite guests.

* If your security requirements allow it, we recommend allowing all users with a userType of Member to invite guests.

* Determine if you want users with a userType of Guest, which is the default account type for Azure AD B2B users, to be able to invite other guests. 

![Screenshot of guest invitation settings.](media/secure-external-access/5-guest-invite-settings.png)

 

### Collect additional information about external users

If you use Azure AD entitlement management, you can configure questions for external users to answer. The questions will then be shown to approvers to help them make a decision. You can configure different sets of questions for each [access package policy](../governance/entitlement-management-access-package-approval-policy.md) so that approvers can have relevant information for the access they're approving. For example, if one access package is intended for vendor access, then the requestor may be asked for their vendor contract number. A different access package intended for suppliers, may ask for their country of origin.

If you use a self-service portal, you can use [API connectors](../external-identities/api-connectors-overview.md) to collect additional attributes about users as they sign up. You can then potentially use those attributes to assign access. For example, if during the sign-up process you collect their supplier ID, you could use that attribute to dynamically assign them to a group or access package for that supplier. You can create custom attributes in the Azure portal and use them in your self-service sign-up user flows. You can also read and write these attributes by using the [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). 

### Troubleshoot invitation redemption to Azure AD users

There are three instances when invited guest users from a collaboration partner using Azure AD will have trouble redeeming an invitation.

* If using an allow list and the user’s domain isn't included in an allow list.

* If the collaboration partner’s home tenant has tenant restrictions that prevent collaboration with external users..

* If the user isn't part of the partner’s Azure AD tenant. For example, there are users at contoso.com who are only in Active Directory (or another on-premises IdP), they'll only be able to redeem invitations via the email OTP process. for more information, see the [invitation redemption flow](../external-identities/redemption-experience.md).

## Control what external users can access

Most organizations aren't monolithic. That is, there are some resources that are fine to share with external users, and some you will not want external users to access. Therefore, you must control what external users access. Consider using [Entitlement management and access packages to control access](6-secure-access-entitlement-managment.md) to specific resources.

By default, guest users can see information and attributes about tenant members and other partners, including group memberships. Consider if your security requirements call for limiting external user access to this information.

![Screenshot of configuring external collaboration settings.](media/secure-external-access/5-external-collaboration-settings.png)

We recommend the following restrictions for guest users.

* **Limit guest access to browsing groups and other properties in the directory**

   * Use the external collaboration settings to restrict guest ability to read groups they aren't members of.

* **Block access to employee-only apps**.

   * Create a Conditional Access policy to block access to Azure AD-integrated applications that are only appropriate for non-guest users. 

* **Block access to the Azure portal. You can make rare necessary exceptions**. 

   * Create a Conditional Access policy that includes either All guest and external users and then [implement a policy to block access](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management).

 

## Remove users who no longer need access

Evaluate current access so that you can [review and remove users who no longer need access](../governance/access-reviews-external-users.md). Include external users in your tenant as guests, and those with member accounts. 

Some organizations added external users such as vendors, partners, and contractors as members. These members may have a specific attribute, or usernames that begin with, for example

* v- for vendors

* p- for partners

* c- for contractors

Evaluate any external users with member accounts to determine if they still need access. If so, transition these users to Azure AD B2B as described in the next section.

You may also have guest users who weren't invited through Entitlement Management or Azure AD B2B

To find these users, you can:

* [Find guest users not invited through Entitlement Management](../governance/access-reviews-external-users.md).

   * We provide a [SAMPLE PowerShell script.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Transition these users to Azure AD B2B users as described in the following section.

## Transition your current external users to B2B

If you haven’t been using Azure AD B2B, you likely have non-employee users in your tenant. We recommend you transition these accounts to Azure AD B2B external user accounts and then change their UserType to Guest. This enables you to take advantage of the many ways Azure AD and Microsoft 365 allow you to treat external users differently. Some of these ways include:

* Easily including or excluding guest users in Conditional Access policies

* Easily including or excluding guest users in Access Packages and Access Reviews

* Easily including or excluding external access to Teams, SharePoint, and other resources.

To transition these internal users while maintaining their current access, UPN, and group memberships, see [Invite external users to B2B collaboration](../external-identities/invite-internal-users.md).

## Decommission undesired collaboration methods

To complete your transition to governed collaboration, you should decommission undesired collaboration methods. Which you decommission is based on the degree of control you wish IT to exert over collaboration, and your security posture. For information about IT versus end-user control, see [Determine your security posture for external access](1-secure-access-posture.md).

The following are collaboration vehicles you may wish to evaluate.

### Direct invitation through Microsoft Teams

By default Teams allows external access, which means that organization can communicate with all external domains. If you want to restrict or allow specific domains just for Teams, you can do so in the [Teams Admin portal](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### Direct sharing through SharePoint and OneDrive

Direct sharing through SharePoint and OneDrive can add users outside of the Entitlement Management process. For an in-depth look at these configurations see [Manage Access with Microsoft Teams, SharePoint, and OneDrive for business](9-secure-access-teams-sharepoint.md)
You can also [block the use of user’s personal OneDrive](https://docs.microsoft.com/office365/troubleshoot/group-policy/block-onedrive-use-from-office) if desired.

### Sending documents through email

Your users will send documents through email to external users. Consider how you want to restrict and encrypt access to these documents by using sensitivity labels. For more information, see Manage access with Sensitivity labels.

### Unsanctioned collaboration tools

The landscape of collaboration tools is vast. Your users likely use many outside of their official duties, including platforms like Google Docs, DropBox, Slack, or Zoom. It's possible to block the use of such tools from a corporate network at the Firewall level and with mobile application management for organization-managed devices. However, this will also block any sanctioned instances of these platforms and wouldn't block access from unmanaged devices. Block platforms you don’t want any use of if necessary, and create business policies for no unsanctioned usage for the platforms you need to use. 

For more information on managing unsanctioned applications, see:

* [Governing connected apps](https://docs.microsoft.com/cloud-app-security/governance-actions)

* [Sanctioning and unsanctioning an application.](https://docs.microsoft.com/cloud-app-security/governance-discovery)

 
### Next steps

See the following articles on securing external access to resources. We recommend you take the actions in the listed order.

1. [Determine your security posture for external access](1-secure-access-posture.md)

2. [Discover your current state](2-secure-access-current-state.md)

3. [Create a governance plan](3-secure-access-plan.md)

4. [Use groups for security](4-secure-access-groups.md)

5. [Transition to Azure AD B2B](5-secure-access-b2b.md) (You are here.)

6. [Secure access with Entitlement Management](6-secure-access-entitlement-managment.md)

7. [Secure access with Conditional Access policies](7-secure-access-conditional-access.md)

8. [Secure access with Sensitivity labels](8-secure-access-sensitivity-labels.md)

9. [Secure access to Microsoft Teams, OneDrive, and SharePoint](9-secure-access-teams-sharepoint.md)