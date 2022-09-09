---
title: Provision apps with scoping filters | Microsoft Docs
description: Learn how to use scoping filters to prevent objects in apps that support automated user provisioning from being provisioned if an object doesn't satisfy your business requirements.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
---

# Attribute-based application provisioning with scoping filters
The objective of this article is to explain how to use scoping filters to define attribute-based rules that determine which users are provisioned to an application.

## Scoping filter use cases

A scoping filter allows the Azure Active Directory (Azure AD) provisioning service to include or exclude any users who have an attribute that matches a specific value. For example, when provisioning users from Azure AD to a SaaS application used by a sales team, you can specify that only users with a "Department" attribute of "Sales" should be in scope for provisioning.

Scoping filters can be used differently depending on the type of provisioning connector:

* **Outbound provisioning from Azure AD to SaaS applications**. When Azure AD is the source system, [user and group assignments](../manage-apps/assign-user-or-group-access-portal.md) are the most common method for determining which users are in scope for provisioning. These assignments also are used for enabling single sign-on and provide a single method to manage access and provisioning. Scoping filters can be used optionally, in addition to assignments or instead of them, to filter users based on attribute values.

    >[!TIP]
    > You can disable provisioning based on assignments for an enterprise application by changing settings in the [Scope](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menu under the provisioning settings to **Sync all users and groups**. 

* **Inbound provisioning from HCM applications to Azure AD and Active Directory**. When an [HCM application such as Workday](../saas-apps/workday-tutorial.md) is the source system, scoping filters are the primary method for determining which users should be provisioned from the HCM application to Active Directory or Azure AD.

By default, Azure AD provisioning connectors do not have any attribute-based scoping filters configured. 

## Scoping filter construction

A scoping filter consists of one or more *clauses*. Clauses determine which users are allowed to pass through the scoping filter by evaluating each user's attributes. For example, you might have one clause that requires that a user's "State" attribute equals "New York", so only New York users are provisioned into the application. 

A single clause defines a single condition for a single attribute value. If multiple clauses are created in a single scoping filter, they're evaluated together by using "AND" logic. This means all clauses must evaluate to "true" in order for a user to be provisioned.

Finally, multiple scoping filters can be created for a single application. If multiple scoping filters are present, they're evaluated together by using "OR" logic. This means that if all the clauses in any of the configured scoping filters evaluate to "true", the user is provisioned.

Each user or group processed by the Azure AD provisioning service is always evaluated individually against each scoping filter.

As an example, consider the following scoping filter:

![Scoping filter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

According to this scoping filter, users must satisfy the following criteria to be provisioned:

* They must be in New York.
* They must work in the Engineering department.
* Their company employee ID must be between 1,000,000 and 2,000,000.
* Their job title must not be null or empty.

## Create scoping filters
Scoping filters are configured as part of the attribute mappings for each Azure AD user provisioning connector. The following procedure assumes that you already set up automatic provisioning for [one of the supported applications](../saas-apps/tutorial-list.md) and are adding a scoping filter to it.

### Create a scoping filter
1. In the [Azure portal](https://portal.azure.com), go to the **Azure Active Directory** > **Enterprise Applications** > **All applications** section.

2. Select the application for which you have configured automatic provisioning: for example, "ServiceNow".

3. Select the **Provisioning** tab.

4. In the **Mappings** section, select the mapping that you want to configure a scoping filter for: for example, "Synchronize Azure Active Directory Users to ServiceNow".

5. Select the **Source object scope** menu.

6. Select **Add scoping filter**.

7. Define a clause by selecting a source **Attribute Name**, an **Operator**, and an **Attribute Value** to match against. The following operators are supported:

   a. **EQUALS**. Clause returns "true" if the evaluated attribute matches the input string value exactly (case sensitive).

   b. **NOT EQUALS**. Clause returns "true" if the evaluated attribute doesn't match the input string value (case sensitive).

   c. **IS TRUE**. Clause returns "true" if the evaluated attribute contains a Boolean value of true.

   d. **IS FALSE**. Clause returns "true" if the evaluated attribute contains a Boolean value of false.

   e. **IS NULL**. Clause returns "true" if the evaluated attribute is empty.

   f. **IS NOT NULL**. Clause returns "true" if the evaluated attribute isn't empty.

   g. **REGEX MATCH**. Clause returns "true" if the evaluated attribute matches a regular expression pattern. For example: ([1-9][0-9]) matches any number between 10 and 99.

   h. **NOT REGEX MATCH**. Clause returns "true" if the evaluated attribute doesn't match a regular expression pattern.
   
   i. **Greater_Than.** Clause returns "true" if the evaluated attribute is greater than the value. The value specified on the scoping filter must be an integer and the attribute on the user must be an integer [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Clause returns "true" if the evaluated attribute is greater than or equal to the value. The value specified on the scoping filter must be an integer and the attribute on the user must be an integer [0,1,2,...]. 
   
   k. **Includes.** Clause returns "true" if the evaluated attribute contains the string value (case sensitive) as described [here](/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - The IsMemberOf filter is not supported currently.
> - EQUALS and NOT EQUALS are not supported for multi-valued attributes

9. Optionally, repeat steps 7-8 to add more scoping clauses.

10. In **Scoping Filter Title**, add a name for your scoping filter.

11. Select **OK**.

12. Select **OK** again on the **Scoping Filters** screen. Optionally, repeat steps 6-11 to add another scoping filter.

13. Select **Save** on the **Attribute Mapping** screen. 

>[!IMPORTANT] 
> Saving a new scoping filter triggers a new full sync for the application, where all users in the source system are evaluated again against the new scoping filter. If a user in the application was previously in scope for provisioning, but falls out of scope, their account is disabled or deprovisioned in the application. To override this default behavior, refer to [Skip deletion for user accounts that go out of scope](../app-provisioning/skip-out-of-scope-deletions.md).


## Common scoping filters
| Target Attribute| Operator | Value | Description|
|----|----|----|----|
|userPrincipalName|REGEX MATCH|.\*@domain.com |All users with userPrincipal that has the domain @domain.com will be in scope for provisioning|
|userPrincipalName|NOT REGEX MATCH|.\*@domain.com|All users with userPrincipal that has the domain @domain.com will be out of scope for provisioning|
|department|EQUALS|sales|All users from the sales department are in scope for provisioning|
|workerID|REGEX MATCH|(1[0-9][0-9][0-9][0-9][0-9][0-9])| All employees with workerIDs between 1000000 and 2000000 are in scope for provisioning.|

## Related articles
* [Automate user provisioning and deprovisioning to SaaS applications](../app-provisioning/user-provisioning.md)
* [Customize attribute mappings for user provisioning](../app-provisioning/customize-application-attributes.md)
* [Write expressions for attribute mappings](functions-for-customizing-application-data.md)
* [Account provisioning notifications](../app-provisioning/user-provisioning.md)
* [Use SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [List of tutorials on how to integrate SaaS apps](../saas-apps/tutorial-list.md)