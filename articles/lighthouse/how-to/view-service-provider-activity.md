---
title: View service provider activity
description: Customers can view logged activity to see actions performed by service providers through Azure delegated resource management.
ms.date: 10/12/2020
ms.topic: how-to
---

# View service provider activity

Customers who have delegated subscriptions for [Azure Lighthouse](../overview.md) can [view Azure Activity log](../../azure-monitor/platform/platform-logs-overview.md) data to see all actions taken. This gives customers full visibility into operations that service providers are performing through [Azure delegated resource management](../concepts/azure-delegated-resource-management.md), along with operations done by users within the customer's own Azure Active Directory (Azure AD) tenant.

> [!TIP]
> We also provide Azure Policy built-in policy definitions to [restrict delegation to specific managing tenants](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) and to [audit delegation of scopes to a managing tenant](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). For more info, see [Audit delegations in your environment](view-manage-service-providers.md#audit-delegations-in-your-environment).

## View activity log data

You can [view the activity log](../../azure-monitor/platform/activity-log.md#view-the-activity-log) from the **Monitor** menu in the Azure portal. To limit results to a specific subscription, use the filters to select a specific subscription. You can also [view and retrieve activity log events](../../azure-monitor/platform/activity-log.md#view-the-activity-log) programmatically.

> [!NOTE]
> Users in a service provider's tenant can view activity log results for a delegated subscription in a customer tenant if they were granted the [Reader](../../role-based-access-control/built-in-roles.md#reader) role (or another built-in role which includes Reader access) when that subscription was onboarded to Azure Lighthouse.

In the activity log, you'll see the name of the operation and its status, along with the date and time it was performed. The **Event initiated by** column shows which user performed the operation, whether it was a user in a service provider's tenant acting through Azure Lighthouse, or a user in the customer's own tenant. Note that the name of the user is shown, rather than the tenant or the role that the user has been assigned for that subscription.

Logged activity is available in the Azure portal for the past 90 days. To learn how to store this data for longer than 90 days, see [Collect and analyze Azure activity logs in Log Analytics workspace](../../azure-monitor/platform/activity-log.md).

> [!NOTE]
> Users from the service provider appear in the activity log, but these users and their role assignments are not shown in **Access Control (IAM)** or when retrieving role assignment info via APIs.

## Set alerts for critical operations

To stay aware of critical operations that service providers (or users in your own tenant) are performing, we recommend creating [activity log alerts](../../azure-monitor/platform/activity-log-alerts.md). For example, you may want to track all administrative actions for a subscription, or be notified when any virtual machine in a particular resource group is deleted. When you create alerts, they will include actions performed by users in the customer's own tenant as well as in any managing tenants.

For more information, see [Create and manage activity log alerts](../../azure-monitor/platform/alerts-activity-log.md).

## Create log queries

You can create queries to analyze your logged activity or focus on specific items. For example, perhaps an audit requires you to report on all administrative-level actions performed on a subscription. You can create a query to filter on only these actions and sort the results by user, date, or another value.

For more information, see [Overview of log queries in Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## Next steps

- Learn more about [Azure Monitor](../../azure-monitor/index.yml).
- Learn how to [view and manage service provider offers](view-manage-service-providers.md) in the Azure portal.
