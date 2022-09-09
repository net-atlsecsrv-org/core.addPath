---
title: Webhook actions for log alerts in Azure Alerts
description: This article describes how to an log alert rule using log analytics workspace or application insights, will push data as HTTP webhook and details of the different customizations possible.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
---

# Webhook actions for log alert rules
When a [log alert is created in Azure](alerts-log.md), you have the option of [configuring using action groups](action-groups.md) to perform one or more actions.  This article describes the different webhook actions that are available and details on configuring the custom JSON-based webhook.

> [!NOTE]
> You can also use the [common alert schema](https://aka.ms/commonAlertSchemaDocs), which provides the advantage of having a single extensible and unified alert payload across all the alert services in Azure Monitor, for your webhook integrations. [Learn about the common alert schema definitions.](https://aka.ms/commonAlertSchemaDefinitions)​

## Webhook actions

Webhook actions allow you to invoke an external process through a single HTTP POST request.  The service being called should support webhooks and determine how it uses any payload it receives.    

Webhook actions require the properties in the following table:

| Property | Description |
|:--- |:--- |
| Webhook URL |The URL of the webhook. |
| Custom JSON payload |Custom payload to send with the webhook, when this option is chosen during alert creation. Details available at [Manage log alerts](alerts-log.md) |

> [!NOTE]
> View Webhook button alongside *Include custom JSON payload for webhook* option for Log Alert, will display sample webhook payload for the customization provided. It does not contain actual data and representative of JSON schema used for Log Alerts. 

Webhooks include a URL and a payload formatted in JSON that is the data sent to the external service.  By default, the payload includes the values in the following table:  You can choose to replace this payload with a custom one of your own.  In that case you can use the variables in the table for each of the parameters to include their value in your custom payload.


| Parameter | Variable | Description |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Name of the alert rule. |
| Severity |#severity |Severity set for the fired log alert. |
| AlertThresholdOperator |#thresholdoperator |Threshold operator for the alert rule.  *Greater than* or *Less than*. |
| AlertThresholdValue |#thresholdvalue |Threshold value for the alert rule. |
| LinkToSearchResults |#linktosearchresults |Link to Analytics portal that returns the records from the query that created the alert. |
| ResultCount |#searchresultcount |Number of records in the search results. |
| Search Interval End time |#searchintervalendtimeutc |End time for the query in UTC, format - mm/dd/yyyy HH:mm:ss AM/PM. |
| Search Interval |#searchinterval |Time window for the alert rule, format - HH:mm:ss. |
| Search Interval StartTime |#searchintervalstarttimeutc |Start time for the query in UTC, format - mm/dd/yyyy HH:mm:ss AM/PM.. 
| SearchQuery |#searchquery |Log search query used by the alert rule. |
| SearchResults |"IncludeSearchResults": true|Records returned by the query as a JSON Table, limited to the first 1,000 records; if "IncludeSearchResults": true is added in custom JSON webhook definition as a top-level property. |
| Alert Type| #alerttype | The type of log alert rule configured - [Metric Measurement](alerts-unified-log.md#metric-measurement-alert-rules) or [Number of Results](alerts-unified-log.md#number-of-results-alert-rules).|
| WorkspaceID |#workspaceid |ID of your Log Analytics workspace. |
| Application ID |#applicationid |ID of your Application Insight app. |
| Subscription ID |#subscriptionid |ID of your Azure Subscription used. 

> [!NOTE]
> LinkToSearchResults passes parameters like SearchQuery, Search Interval StartTime & Search Interval End time in the URL to Azure portal for viewing in Analytics section. Azure portal has URI size limit of approximately 2000 characters and will *not* open link provided in alerts, if parameters values exceed the said limit. Users can manually input details to view results in Analytics portal or use the [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) or [Log Analytics REST API](/rest/api/loganalytics/) to retrieve results programmatically 

For example, you might specify the following custom payload that includes a single parameter called *text*.  The service that this webhook calls would be expecting this parameter.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
This example payload would resolve to something like the following when sent to the webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
As all variables in a custom webhook have to specified within JSON enclosure like "#searchinterval", the resultant webhook will also have variable data inside enclosure like "00:05:00".

To include search results in a custom payload, ensure that **IncludeSearchResults** is set as a top-level property in the json payload. 

## Sample payloads
This section shows sample payload for webhook for Log Alerts, including when payload is standard and when its custom.

### Standard Webhook for Log Alerts 
Both of these examples have stated a dummy payload with only two columns and two rows.

#### Log Alert for Azure Log-Analytics
Following is a sample payload for a standard webhook action *without custom Json option* being used for  log analytics-based alerts.

```json
{
	"SubscriptionId":"12345a-1234b-123c-123d-12345678e",
	"AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
	"SearchResult":
        {
		"tables":[
                    {"name":"PrimaryResult","columns":
                        [
				        {"name":"$table","type":"string"},
					    {"name":"Id","type":"string"},
					    {"name":"TimeGenerated","type":"datetime"}
                        ],
					"rows":
                        [
						    ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
	"WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> Severity field value might change if you have [switched your API preference](alerts-log-api-switch.md) for log alerts on Log Analytics.


#### Log Alert for Azure Application Insights
Following is a sample payload for a standard webhook *without custom Json option* when used for application insights-based log-alerts.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
	"SubscriptionId":"12345a-1234b-123c-123d-12345678e",
	"AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
	"SearchResult":
        {
		"tables":[
                    {"name":"PrimaryResult","columns":
                        [
				        {"name":"$table","type":"string"},
					    {"name":"Id","type":"string"},
					    {"name":"TimeGenerated","type":"datetime"}
                        ],
					"rows":
                        [
						    ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### Log Alert with custom JSON Payload
For example, to create a custom payload that includes just the alert name and the search results, you could use the following: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Following is a sample payload for a custom webhook action for any log alert.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
	"SearchResult":
        {
		"tables":[
                    {"name":"PrimaryResult","columns":
                        [
				        {"name":"$table","type":"string"},
					    {"name":"Id","type":"string"},
					    {"name":"TimeGenerated","type":"datetime"}
                        ],
					"rows":
                        [
						    ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## Next steps
- Learn about [Log Alerts in Azure Alerts](alerts-unified-log.md)
- Understand [managing log alerts in Azure](alerts-log.md)
- Create and manage [action groups in Azure](action-groups.md)
- Learn more about [Application Insights](../../azure-monitor/app/analytics.md)
- Learn more about [log queries](../log-query/log-query-overview.md). 

