---
title: Understand admin roles for Enterprise Agreements (EA) in Azure
description: Learn about Enterprise administrator roles in Azure. You can assign five distinct administrative roles.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: banders
ms.custom: contperf-fy21q1
---

# Managing Azure Enterprise Agreement roles

To help manage your organization's usage and spend, Azure customers with an Enterprise Agreement can assign five distinct administrative roles:

- Enterprise Administrator
- Enterprise Administrator (read only)<sup>1</sup>
- Department Administrator
- Department Administrator (read only)
- Account Owner<sup>2</sup>

<sup>1</sup> The Bill-To contact of the EA contract will be under this role.

<sup>2</sup> The Bill-To contact cannot be added or changed in the Azure EA Portal and will be added to the EA enrollment based on the user who is set up as the Bill-To contact on agreement level. To change the Bill-To contact, a request needs to be made through a partner/software advisor to the Regional Operations Center (ROC).

The first enrollment administrator that is set up during the enrollment provisioning determines the authentication type of the Bill-to contact account. When the bill-to contact gets added to the EA Portal as a read-only administrator, they are given Microsoft account authentication. 

For example, if the initial authentication type is set to Mixed, the EA will be added as a Microsoft account and the Bill-to contact will have read-only EA admin privileges. If the EA admin doesn’t approve Microsoft account authorization for an existing Bill-to contact, the EA admin may delete the user in question and ask the customer to add the user back as a read-only administrator with a Work or School account Only set at enrollment level in the EA portal.

These roles are specific to managing Azure Enterprise Agreements and are in addition to the built-in roles Azure has to control access to resources. For more information, see [Azure built-in roles](../../role-based-access-control/built-in-roles.md).

## Azure Enterprise portal hierarchy

The Azure Enterprise portal hierarchy consists of:

- **Azure Enterprise portal** - an online management portal that helps you manage costs for your Azure EA services. You can:

  - Create an Azure EA hierarchy with departments, accounts, and subscriptions.
  - Reconcile the costs of your consumed services, download usage reports, and view price lists.
  - Create API keys for your enrollment.

- **Departments** help you segment costs into logical groupings. Departments enable you to set a budget or quota at the department level.

- **Accounts** are organizational units in the Azure Enterprise portal. You can use accounts to manage subscriptions and access reports.

- **Subscriptions** are the smallest unit in the Azure Enterprise portal. They're containers for Azure services managed by the service administrator.

The following diagram illustrates simple Azure EA hierarchies.

![Diagram of simple Azure EA hierarchies](./media/understand-ea-roles/ea-hierarchies.png)

## Enterprise user roles

The following administrative user roles are part of your enterprise enrollment:

- Enterprise administrator
- Department administrator
- Account owner
- Service administrator
- Notification contact

Roles work in two different portals to complete tasks. You use the [Azure Enterprise portal](https://ea.azure.com) to manage billing and costs, and the [Azure portal](https://portal.azure.com) to manage Azure services.

User roles are associated with a user account. To validate user authenticity, each user must have a valid work, school, or Microsoft account. Ensure that each account is associated with an email address that's actively monitored. Account notifications are sent to the email address.

When setting up users, you can assign multiple accounts to the enterprise administrator role. However, only one account can hold the account owner role. Also, you can assign both the enterprise administrator and account owner roles to a single account.

### Enterprise administrator

Users with this role have the highest level of access. They can:

- Manage accounts and account owners.
- Manage other enterprise administrators.
- Manage department administrators.
- Manage notification contacts.
- View usage across all accounts.
- View unbilled charges across all accounts.
- View and manage all reservation orders and reservations that apply to the Enterprise Agreement.
  - Enterprise administrator (read-only) can view reservation orders and reservations. They can't manage them.

You can have multiple enterprise administrators in an enterprise enrollment. You can grant read-only access to enterprise administrators. They all inherit the department administrator role.

### Department administrator

Users with this role can:

- Create and manage departments.
- Create new account owners.
- View usage details for the departments that they manage.
- View costs, if they have the necessary permissions.

You can have multiple department administrators for each enterprise enrollment.

You can grant department administrators read-only access when you edit or create a new department administrator. Set the read-only option to **Yes**.

### Account owner

Users with this role can:

- Create and manage subscriptions.
- Manage service administrators.
- View usage for subscriptions.

Each account requires a unique work, school, or Microsoft account. For more information about Azure Enterprise portal administrative roles, see [Understand Azure Enterprise Agreement administrative roles in Azure](understand-ea-roles.md).

### Service administrator

The service administrator role has permissions to manage services in the Azure portal and assign users to the coadministrator role.

### Notification contact

The notification contact receives usage notifications related to the enrollment.

The following sections describe the limitations and capabilities of each role.

## User limit for admin roles

|Role| User limit|
|---|---|
|Enterprise Administrator|Unlimited|
|Enterprise Administrator (read only)|Unlimited|
|Department Administrator|Unlimited|
|Department Administrator (read only)|Unlimited|
|Account Owner|1 per account<sup>3</sup>|

<sup>3</sup> Each account requires a unique Microsoft account, or work or school account.

## Organization structure and permissions by role

|Tasks| Enterprise Administrator|Enterprise Administrator (read only)|Department Administrator|Department Administrator (read only)|Account Owner| Partner|
|---|---|---|---|---|---|---|
|View Enterprise Administrators|✔|✔|✘|✘|✘|✔|
|Add or remove Enterprise Administrators|✔|✘|✘|✘|✘|✘|
|View Notification Contacts<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Add or remove Notification Contacts<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Create and manage Departments |✔|✘|✘|✘|✘|✘|
|View Department Administrators|✔|✔|✔|✔|✘|✔|
|Add or remove Department Administrators|✔|✘|✔|✘|✘|✘|
|View Accounts in the enrollment |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Add Accounts to the enrollment and change Account Owner|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Create and manage subscriptions and subscription permissions|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Notification contacts are sent email communications about the Azure Enterprise Agreement.
- <sup>5</sup> Task is limited to accounts in your department.

## Add a new enterprise administrator

Enterprise administrators have the most privileges when managing an Azure EA enrollment. The initial Azure EA admin was created when the EA agreement was set up. However, you can add or remove new admins at any time. New admins are only added by existing admins. For more information about adding  additional enterprise admins, see [Create another enterprise admin](ea-portal-administration.md#create-another-enterprise-administrator). For more information about billing profile roles and tasks, see [Billing profile roles and tasks](understand-mca-roles.md#billing-profile-roles-and-tasks).

## Update account owner state from pending to active

When new Account Owners (AO) are added to an Azure EA enrollment for the first time, their status appears as _pending_. When a new account owner receives the activation welcome email, they can sign in to activate their account. Once they activate their account, the account status is updated from _pending_ to _active_. The account owner needs to read the 'Warning' message and select **Continue**. New users might get prompted enter their first and last name to create a Commerce Account. If so, they must add the required information to continue and then the account is activated.

## Add a department Admin

After an Azure EA admin creates a department, the Azure Enterprise administrator can add department administrators and associate each one to a department. A department administrator can create new accounts. New accounts are needed for Azure EA subscriptions to get created.

For more information about adding a department admin, see [Create an Azure EA department admin](ea-portal-administration.md#add-a-department-administrator).

## Usage and costs access by role

|Tasks| Enterprise Administrator|Enterprise Administrator (read only)|Department Administrator|Department Administrator (read only) |Account Owner| Partner|
|---|---|---|---|---|---|---|
|View credit balance including Azure Prepayment|✔|✔|✘|✘|✘|✔|
|View department spending quotas|✔|✔|✘|✘|✘|✔|
|Set department spending quotas|✔|✘|✘|✘|✘|✘|
|View organization's EA price sheet|✔|✔|✘|✘|✘|✔|
|View usage and cost details|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Manage resources in Azure portal|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Requires that the Enterprise Administrator enable **DA view charges** policy in the Enterprise portal. The Department Administrator can then see cost details for the department.
- <sup>7</sup> Requires that the Enterprise Administrator enable **AO view charges** policy in the Enterprise portal. The Account Owner can then see cost details for the account.

## See pricing for different user roles

You may see different pricing in the Azure portal depending on your administrative role and how the view charges policies are set by the Enterprise Administrator. The two policies in the Enterprise portal that affect the pricing you see in the Azure portal are:

- DA view charges
- AO view charges

To learn how to set these policies, see [Manage access to billing information for Azure](manage-billing-access.md).

The following table shows the relationship between the Enterprise Agreement admin roles, the view charges policy, the Azure role in the Azure portal, and the pricing that you see in the Azure portal. The Enterprise Administrator always sees usage details based on the organization's EA pricing. However, the Department Administrator and Account Owner see different pricing views based on the view charge policy and their Azure role. The Department Admin role listed in the following table refers to both Department Admin and Department Admin (read only) roles.

|Enterprise Agreement admin role|View charges policy for role|Azure role|Pricing view|
|---|---|---|---|
|Account Owner OR Department Admin|✔ Enabled|Owner|Organization's EA pricing|
|Account Owner OR Department Admin|✘ Disabled|Owner|Retail pricing|
|Account Owner OR Department Admin|✔ Enabled |none|No pricing|
|Account Owner OR Department Admin|✘ Disabled |none|No pricing|
|None|Not applicable |Owner|Retail pricing|

You set the Enterprise admin role and view charges policies in the Enterprise portal. The Azure role can be updated in the Azure portal. For more information, see [Manage access using RBAC and the Azure portal](../../role-based-access-control/role-assignments-portal.md).



## Next steps

- [Manage access to billing information for Azure](manage-billing-access.md)
- [Manage access using RBAC and the Azure portal](../../role-based-access-control/role-assignments-portal.md)
- [Azure built-in roles](../../role-based-access-control/built-in-roles.md)
