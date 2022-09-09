---
title: View service provider activity
description: Customers can view logged activity to see actions performed by service providers through Azure delegated resource management.
ms.date: 12/6/2019
ms.topic: conceptual
---

# View service provider activity

Customers who have delegated subscriptions for Azure delegated resource management can [view Azure Activity log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) data to see all actions taken. This gives customers full visibility into operations that service providers are performing through Azure delegated resource management, along with those done by users within the customer's own Azure Active Directory (Azure AD) tenant.

## View activity log data

You can [view the activity log](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) from the **Monitor** menu in the Azure portal. To limit results to a specific subscription, you can use the filters to select a specific subscription. You can also [view and retrieve activity log events](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) programmatically.

> [!NOTE]
> Users in a service provider's tenant can view activity log results for a delegated subscription in a customer tenant if they were granted the [Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) role (or another built-in role which includes Reader access) when that subscription was onboarded for Azure delegated resource management.

In the activity log, you'll see the name of the operation and its status, along with the date and time it was performed. The **Event initiated by** column shows which user performed the operation, whether it was a user in a service provider's tenant acting through Azure delegated resource management, or a user in the customer's own tenant. Note that the name of the user is shown, rather than the tenant or the role that the user has been assigned for that subscription.

Logged activity is available in the Azure portal for the past 90 days. To learn how to store this data for longer than 90 days, see [Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## Set alerts for critical operations

To stay aware of critical operations that service providers (or users in your own tenant) are performing, we recommend creating [activity log alerts](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). For example, you may want to track all administrative actions for a subscription, or be notified when any virtual machine in a particular resource group is deleted. When you create alerts, they will include actions taken by users in the customer's own tenant as well as in any managing tenants.

For more information, see [Create and manage activity log alerts](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## Create log queries

You can create queries to analyze your logged activity or focus on specific items. For example, perhaps an audit requires you to report on all administrative-level actions performed on a subscription. You can create a query to filter on only these actions and sort the results by user, date, or another value.

For more information, see [Overview of log queries in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## Next steps

- Learn more about [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/).
- Learn how to [view and manage service provider offers](view-manage-service-providers.md) in the Azure portal.