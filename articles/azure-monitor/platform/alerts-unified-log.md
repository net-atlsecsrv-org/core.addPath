---
title: Log alerts in Azure Monitor
description: Trigger emails, notifications, call websites URLs (webhooks), or automation when the analytic query conditions you specify are met for Azure Alerts.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: vinagara
ms.subservice: alerts
---
# Log alerts in Azure Monitor

This article provides details of Log alerts are one of the types of alerts supported within the [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) and allow users to use Azure's analytics platform as basis for alerting.

Log Alert consists of Log Search rules created for [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) or [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). To learn more about its usage, see [creating log alerts in Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Popular log data from [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) is now also available on the metric platform in Azure Monitor. For details view, [Metric Alert for Logs](../../azure-monitor/platform/alerts-metric-logs.md)


## Log search alert rule - definition and types

Log search rules are created by Azure Alerts to automatically run specified log queries at regular intervals.  If the results of the log query match particular criteria, then an alert record is created. The rule can then automatically run one or more actions using [Action Groups](../../azure-monitor/platform/action-groups.md). [Azure Monitoring Contributor](../../azure-monitor/platform/roles-permissions-security.md) role for creating, modifying, and updating log alerts may be required; along with access & query execution rights for the analytics target(s) in alert rule or alert query. If the user creating doesn't have access to all analytics target(s) in alert rule or alert query - the rule creation may fail or the log alert rule will be executed with partial results.

Log search rules are defined by the following details:

- **Log Query**.  The query that runs every time the alert rule fires.  The records returned by this query are used to determine whether an alert is to be triggered. Analytics query can be for a specific Log Analytics workspace or Application Insights app and even span across [multiple Log Analytics and Application Insights resources](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) provided the user has access as well as query rights to all resources. 
    > [!IMPORTANT]
    > Log alert **do not** support use of [functions](../log-query/functions.md) for security reasons. Also [cross-resource query](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) support in log alerts for Application Insights and log alerts for [Log Analytics configured using scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) only.

    Some analytic commands and combinations are incompatible with use in log alerts; for more details view, [Log alert queries in Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Time Period**.  Specifies the time range for the query. The query returns only records that were created within this range of the current time. Time period restricts the data fetched for log query to prevent abuse and circumvents any time command (like ago) used in log query. <br>*For example, If the time period is set to 60 minutes, and the query is run at 1:15 PM, only records created between 12:15 PM and 1:15 PM is returned to execute log query. Now if the log query uses time command like ago (7d), the log query would be run only for data between 12:15 PM and 1:15 PM - as if data exists for only the past 60 minutes. And not for seven days of data as specified in log query.*

- **Frequency**.  Specifies how often the query should be run. Can be any value between 5 minutes and 24 hours. Should be equal to or less than the time period.  If the value is greater than the time period, then you risk records being missed.<br>*For example, consider a time period of 30 minutes and a frequency of 60 minutes.  If the query is run at 1:00, it returns records between 12:30 and 1:00 PM.  The next time the query would run is 2:00 when it would return records between 1:30 and 2:00.  Any records created between 1:00 and 1:30 would never be evaluated.*

- **Threshold**.  The results of the log search are evaluated to determine whether an alert should be created.  The threshold is different for the different types of log search alert rules.

Log search rules be it for [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) or [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), can be of two types. Each of these types is described in detail in the sections that follow.

- **[Number of results](#number-of-results-alert-rules)**. Single alert created when the number records returned by the log search exceed a specified number.
- **[Metric measurement](#metric-measurement-alert-rules)**.  Alert created for each object in the results of the log search with values that exceed specified threshold.

The differences between alert rule types are as follows.

- *Number of results* alert rules always creates a single alert, while *Metric measurement* alert rule creates an alert for each object that exceeds the threshold.
- *Number of results* alert rules create an alert when the threshold is exceeded a single time. *Metric measurement* alert rules can create an alert when the threshold is exceeded a certain number of times over a particular time interval.

### Number of results alert rules

**Number of results** alert rules create a single alert when the number of records returned by the search query exceed the specified threshold. This type of alert rule is ideal for working with events such as Windows event logs, Syslog, WebApp Response, and Custom logs.  You may want to create an alert when a particular error event gets created, or when multiple error events are created within a particular time period.

**Threshold**: The threshold for a Number of results alert rules is greater than or less than a particular value.  If the number of records returned by the log search match this criteria, then an alert is created.

To alert on a single event, set the number of results to greater than 0 and check for the occurrence of a single event that was created since the last time the query was run. Some applications may log an occasional error that shouldn't necessarily raise an alert.  For example, the application may retry the process that created the error event and then succeed the next time.  In this case, you may not want to create the alert unless multiple events are created within a particular time period.  

In some cases, you may want to create an alert in the absence of an event.  For example, a process may log regular events to indicate that it's working properly.  If it doesn't log one of these events within a particular time period, then an alert should be created.  In this case, you would set the threshold to **less than 1**.

#### Example of Number of Records type log alert

Consider a scenario where you want to know when your web-based App gives a response to users with code 500 (that is) Internal Server Error. You would create an alert rule with the following details:  

- **Query:** requests | where resultCode == "500"<br>
- **Time period:** 30 minutes<br>
- **Alert frequency:** five minutes<br>
- **Threshold value:** Greater than 0<br>

Then alert would run the query every 5 minutes, with 30 minutes of data - to look for any record where result code was 500. If even one such record is found, it fires the alert and triggers the action configured.

### Metric measurement alert rules

**Metric measurement** alert rules create an alert for each object in a query with a value that exceeds a specified threshold.  They have the following distinct differences from **Number of results** alert rules.

- **Aggregate function**: Determines the calculation that is performed and potentially a numeric field to aggregate.  For example, **count()** returns the number of records in the query, **avg(CounterValue)** returns the average of the CounterValue field over the interval. Aggregate function in query must be named/called: AggregatedValue and provide a numeric value. 

- **Group Field**: A record with an aggregated value is created for each instance of this field, and an alert can be generated for each.  For example, if you wanted to generate an alert for each computer, you would use **by Computer**. In case, there are multiple group fields specified in alert query, user can specify which field to be used to sort results by using the **Aggregate On** (metricColumn) parameter

    > [!NOTE]
    > *Aggregate On* (metricColumn) option is available for Metric Measurement type log alerts for Application Insights and log alerts for [Log Analytics configured using scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) only.

- **Interval**:  Defines the time interval over which the data is aggregated.  For example, if you specified **five minutes**, a record would be created for each instance of the group field aggregated at 5-minute intervals over the time period specified for the alert.

    > [!NOTE]
    > Bin function must be used in query to specify interval. As bin() can result in unequal time intervals  - Alert will automatically convert bin command to  bin_at command with appropriate time at runtime, to ensure results with a fixed point. Metric measurement type of log alert is designed to work with queries having up to three instances of bin() command
    
- **Threshold**: The threshold for Metric measurement alert rules is defined by an aggregate value and a number of breaches.  If any data point in the log search exceeds this value, it's considered a breach.  If the number of breaches in for any object in the results exceeds the specified value, then an alert is created for that object.

Misconfiguration of the *Aggregate On* or *metricColumn* option can cause alert rules to misfire. For more information, see [troubleshooting when metric measurement alert rule is incorrect](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### Example of Metric Measurement type log alert

Consider a scenario where you wanted an alert if any computer exceeded processor utilization of 90% three times over 30 minutes.  You would create an alert rule with the following details:  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **Time period:** 30 minutes<br>
- **Alert frequency:** five minutes<br>
- **Alert Logic - Condition & Threshold:** Greater than 90<br>
- **Group Field (Aggregate-on):** Computer
- **Trigger alert based on:** Total breaches Greater than 2<br>

The query would create an average value for each computer at 5-minute intervals.  This query would be run every 5 minutes for data collected over the previous 30 minutes. Since the Group Field (Aggregate-on) chosen is columnar 'Computer' -  the AggregatedValue is split for various values of 'Computer' and average processor utilization for each computer is determined for a time bin of 5 minutes.  Sample query result for (say) three computers, would be as below.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

If query result was to be plotted, it would appear as.

![Sample query results](media/alerts-unified-log/metrics-measurement-sample-graph.png)

In this example, we see in bins of 5 mins for each of the three computers - average processor utilization as computed for 5 mins. Threshold of 90 being breached by srv01 only once at 1:25 bin. In comparison, srv02 exceeds 90 threshold at 1:10, 1:15 and 1:25 bins; while srv03 exceeds 90 threshold at 1:10, 1:15, 1:20 and 1:30.
Since alert is configured to trigger based on total breaches are more than two, we see that srv02 and srv03 only meet the criteria. Hence separate alerts would be created for srv02 and srv03 since they breached the 90% threshold twice across multiple time bins.  If the *Trigger alert based on:* parameter was instead configured for *Continuous breaches* option, then an alert would be fired **only** for srv03 since it breached the threshold for three consecutive time bins from 1:10 to 1:20. And **not** for srv02, as it breached the threshold for two consecutive time bins from 1:10 to 1:15.

## Log search alert rule - firing and state

Log search alert rule works on the logic predicated by user as per configuration and the custom analytics query used. Since the logic of the exact condition or reason why the alert rule should trigger is encapsulated in an Analytics query - which can differ in each log alert rule. Azure Alerts has scarce info of the specific underlying root-cause inside the log results when the threshold condition of log search alert rule is met or exceeded. Thus log alerts are referred to as state-less and will fire every time the log search result is sufficient to exceed the threshold specified in log alerts of *number of results* or *metric measurement* type of condition. And log alert rules will continually keep firing, as long as the alert condition is met by the result of custom analytics query provided; without the alert every getting resolved. As the logic of the exact root-cause of monitoring failure is masked inside the analytics query provided by the user; there is no means by which Azure Alerts to conclusively deduce whether log search result not meeting threshold indicates resolution of the issue.

Now assume we have a log alert rule called *Contoso-Log-Alert*, as per configuration in the [example provided for Number of Results type log alert](#example-of-number-of-records-type-log-alert). 
- At 1:05 PM when Contoso-Log-Alert was executed by Azure alerts, the log search result yielded 0 records; below the threshold and hence not firing the alert. 
- At the next iteration at 1:10 PM when Contoso-Log-Alert was executed by Azure alerts, log search result provided 5 records; exceeding the threshold and firing the alert, soon after by triggering the [action group](../../azure-monitor/platform/action-groups.md) associated. 
- At 1:15 PM when Contoso-Log-Alert was executed by Azure alerts, log search result provided 2 records; exceeding the threshold and firing the alert, soon after by triggering the [action group](../../azure-monitor/platform/action-groups.md) associated.
- Now at the next iteration at 1:20 PM when Contoso-Log-Alert was executed by Azure alert, log search result provided again 0 records; below the threshold and hence not firing the alert.

But in the above listed case, at 1:15 PM - Azure alerts can't determine that the underlying issues seen at 1:10 persist and if there is net new failures; as query provided by user may be taking into account earlier records - Azure alerts can be sure. Hence to err on the side of caution, when Contoso-Log-Alert is executed at 1:15 PM, configured [action group](../../azure-monitor/platform/action-groups.md) is fired again. Now at 1:20 PM when no records are seen - Azure alerts can't be certain that the cause of the records has been solved; hence Contoso-Log-Alert will not changed to Resolved in Azure Alert dashboard and/or notifications sent out stating resolution of alert.


## Pricing and Billing of Log Alerts

Pricing applicable for Log Alerts is stated at the [Azure Monitor Pricing](https://azure.microsoft.com/pricing/details/monitor/) page. In Azure bills, Log Alerts are represented as type `microsoft.insights/scheduledqueryrules` with:

- Log Alerts on Application Insights shown with exact alert name along with resource group and alert properties
- Log Alerts on Log Analytics shown with exact alert name along with resource group and alert properties; when created using [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

The [legacy Log Analytics API](../../azure-monitor/platform/api-alerts.md) has alert actions and schedules as part of Log Analytics Saved Search and not proper [Azure Resources](../../azure-resource-manager/resource-group-overview.md). Hence to enable billing for such legacy log alerts created for Log Analytics using of Azure portal **without** [switching to new API](../../azure-monitor/platform/alerts-log-api-switch.md) or via [legacy Log Analytics API](../../azure-monitor/platform/api-alerts.md) - hidden pseudo alert rules are created on `microsoft.insights/scheduledqueryrules` for billing on Azure. The hidden pseudo alert rules created for billing on `microsoft.insights/scheduledqueryrules` as shown as `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` along with resource group and alert properties.

> [!NOTE]
> If invalid characters such as `<, >, %, &, \, ?, /` are present, they will be replaced with `_` in the hidden pseudo  alert rule name and hence also in the Azure bill.

To remove the hidden scheduleQueryRules resources created for billing of alert rules using [legacy Log Analytics API](api-alerts.md), user can do any of the following:

- Either user can [switch API preference for the alert rules on the Log Analytics workspace](../../azure-monitor/platform/alerts-log-api-switch.md) and with no loss of their alert rules or monitoring move to Azure Resource Manager compliant [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Thereby eliminate the need to make pseudo hidden alert rules for billing.
- Or if the user doesn't want to switch API preference, the user will need to **delete** the original schedule and alert action using [legacy Log Analytics API](api-alerts.md) or delete in [Azure portal the original log alert rule](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

## Next steps

* Learn about [creating in log alerts in Azure](../../azure-monitor/platform/alerts-log.md).
* Understand [webhooks in log alerts in Azure](alerts-log-webhook.md).
* Learn about [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Learn more about [Application Insights](../../azure-monitor/app/analytics.md).
* Learn more about [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).