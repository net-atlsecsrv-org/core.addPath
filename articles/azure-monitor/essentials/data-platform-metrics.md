---
title: Metrics in Azure Monitor | Microsoft Docs
description: Describes metrics in Azure Monitor which are lightweight monitoring data capable of supporting near real-time scenarios.
documentationcenter: ''
author: bwren
manager: carmonm


ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
---

# Azure Monitor Metrics overview
Azure Monitor Metrics is a feature of Azure Monitor that collects numeric data from [monitored resources](../monitor-reference.md) into a time series database. Metrics are numerical values that are collected at regular intervals and describe some aspect of a system at a particular time. Metrics in Azure Monitor are lightweight and capable of supporting near real-time scenarios making them particularly useful for alerting and fast detection of issues. You can analyze them interactively with metrics explorer, be proactively notified with an alert when a value crosses a threshold, or visualize them in a workbook or dashboard.


> [!NOTE]
> Azure Monitor Metrics is one half of the data platform supporting Azure Monitor. The other is [Azure Monitor Logs](../logs/data-platform-logs.md) which collects and organizes log and performance data and allows it to be analyzed with a rich query language. Metrics are more lightweight than data in Azure Monitor Logs and capable of supporting near real-time scenarios making them particularly useful for alerting and fast detection of issues. Metrics though can only store numeric data in a particular structure, while Logs can store a variety of different data types each with their own structure. You can also perform complex analysis on Logs data using log queries which cannot be used for analysis of Metrics data.


## What can you do with Azure Monitor Metrics?
The following table lists the different ways that you can use Metrics in Azure Monitor.

|  | Description |
|:---|:---|
| **Analyze** | Use [metrics explorer](metrics-charts.md) to analyze collected metrics on a chart and compare metrics from different resources. |
| **Alert** | Configure a [metric alert rule](../alerts/alerts-metric.md) that sends a notification or takes [automated action](../alerts/action-groups.md) when the metric value crosses a threshold. |
| **Visualize** | Pin a chart from metrics explorer to an [Azure dashboard](../app/tutorial-app-dashboards.md).<br>Create a [workbook](../visualize/workbooks-overview.md) to combine with multiple sets of data in an interactive report.Export the results of a query to [Grafana](../visualize/grafana-plugin.md) to leverage its dashboarding and combine with other data sources. |
| **Automate** |  Use [Autoscale](../autoscale/autoscale-overview.md) to increase or decrease resources based on a metric value crossing a threshold. |
| **Retrieve** | Access metric values from a command line using  [PowerShell cmdlets](/powershell/module/az.monitor)<br>Access metric values from custom application using [REST API](./rest-api-walkthrough.md).<br>Access metric values from a command line using  [CLI](/cli/azure/monitor/metrics). |
| **Export** | [Route Metrics to Logs](./resource-logs.md#send-to-azure-storage) to analyze data in Azure Monitor Metrics together with data in Azure Monitor Logs and to store metric values for longer than 93 days.<br>Stream Metrics to an [Event Hub](./stream-monitoring-data-event-hubs.md) to route them to external systems. |
| **Archive** | [Archive](./platform-logs-overview.md) the performance or health history of your resource for compliance, auditing, or offline reporting purposes. |

![Metrics overview](media/data-platform-metrics/metrics-overview.png)


## Data collection
There are three fundamental sources of metrics collected by Azure Monitor. Once these metrics are collected in the Azure Monitor metric database, they can be evaluated together regardless of their source.

**Azure resources**. Platform metrics are created by Azure resources and give you visibility into their health and performance. Each type of resource creates a [distinct set of metrics](./metrics-supported.md) without any configuration required. Platform metrics are collected from Azure resources at one-minute frequency unless specified otherwise in the metric's definition. 

**Applications**. Metrics are created by Application Insights for your monitored applications and help you detect performance issues and track trends in how your application is being used. This includes such values as _Server response time_ and _Browser exceptions_.

**Virtual machine agents**. Metrics are collected from the guest operating system of a virtual machine. Enable guest OS metrics for Windows virtual machines with [Windows Diagnostic Extension (WAD)](../agents/diagnostics-extension-overview.md) and for Linux virtual machines with [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Custom metrics**. You can define metrics in addition to the standard metrics that are automatically available. You can [define custom metrics in your application](../app/api-custom-events-metrics.md) that's monitored by Application Insights or create custom metrics for an Azure service using the [custom metrics API](./metrics-store-custom-rest-api.md).

- See [What is monitored by Azure Monitor?](../monitor-reference.md) for a complete list of data sources that can send data to Azure Monitor Metrics.

## Metrics explorer
Use [Metrics Explorer](metrics-charts.md) to interactively analyze the data in your metric database and chart the values of multiple metrics over time. You can pin the charts to a dashboard to view them with other visualizations. You can also retrieve metrics by using the [Azure monitoring REST API](./rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform-metrics/metrics-explorer.png)

- See [Getting started with Azure Monitor metrics explorer](./metrics-getting-started.md) to get started using metrics explorer.

## Data structure
Data collected by Azure Monitor Metrics is stored in a time-series database which is optimized for analyzing time-stamped data. Each set of metric values is a time series with the following properties:

* The time the value was collected
* The resource the value is associated with
* A namespace that acts like a category for the metric
* A metric name
* The value itself
* Some metrics may have multiple dimensions as described in [Multi-dimensional metrics](#multi-dimensional-metrics). Custom metrics can have up to 10 dimensions.

## Multi-dimensional metrics
One of the challenges to metric data is that it often has limited information to provide context for collected values. Azure Monitor addresses this challenge with multi-dimensional metrics. Dimensions of a metric are name-value pairs that carry additional data to describe the metric value. For example, a metric _Available disk space_ could have a dimension called _Drive_ with values _C:_, _D:_, which would allow viewing either available disk space across all drives or for each drive individually.

The example below illustrates two datasets for a hypothetical metric called _Network Throughput_. The first dataset has no dimensions. The second dataset shows the values with two dimensions, _IP Address_ and _Direction_:

### Network Throughput

| Timestamp     | Metric Value |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

This non-dimensional metric can only answer a basic question like "what was my network throughput at a given time????

### Network Throughput + two dimensions ("IP" and "Direction")

| Timestamp     | Dimension "IP"   | Dimension "Direction" | Metric Value|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100.1 Kbps |

This metric can answer questions such as "what was the network throughput for each IP address?", and "how much data was sent versus received?" Multi-dimensional metrics carry additional analytical and diagnostic value compared to non-dimensional metrics.

### View multi-dimensional performance counter metrics in metrics explorer 
It is not possible to send performance counter metrics that contain an asterisk (\*) to Azure Monitor via the Classic Guest Metrics API. This API cannot display metrics that contain an asterisk because it is a multi-dimensional metric, which Classic metrics do not support.
Below are the instructions on how to configure and view multi-dimensional performance counter metrics:
1.	Go to the diagnostic settings page for your Virtual Machine
2.	Select the ???Performance counters??? tab. 
3.	Click on ???Custom??? to configure the performance counters you would like to collect.
![Screenshot of performance counters section of diagnostic setting page](media/data-platform-metrics/azure-monitor-perf-counter.png)

4.	After you have configured your performance counters, click on ???Sinks???. Then select enable to send your data to Azure Monitor.
![Screenshot of sinks section of diagnostic setting page](media/data-platform-metrics/azure-monitor-sink.png)

5.	To view your metric in Azure Monitor, select ???Virtual Machine Guest??? in the metric namespace dropdown.
![Screenshot of metric namespace](media/data-platform-metrics/vm-guest-namespace.png)

6.	Split metric by instance to see the metric broken down by each of the possible values represented by the "\*" in the configuration.  In this example, the "\*" represents the different logical disk volumes plus the total.
![Screenshot of splitting metric by instance](media/data-platform-metrics/split-by-instance.png)



## Retention of Metrics
For most resources in Azure, platform metrics are stored for 93 days. There are some exceptions:

**Guest OS metrics**
-	**Classic guest OS metrics** - 14 days and sometimes more. These are performance counters collected by the [Windows Diagnostic Extension (WAD)](../agents/diagnostics-extension-overview.md) or the [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) and routed to an Azure storage account. Retention for these metrics is guaranteed to be at least 14 days, though no actual expiration date is written to the storage account. For performance reasons, the portal limits how much data it displays based on volume. Therefore, the actual number of days retrieved by the portal can be longer than 14 days if the volume of data being written is not very large.  
-	**Guest OS metrics sent to Azure Monitor Metrics** - 93 days. These are performance counters collected by the [Windows Diagnostic Extension (WAD)](../agents/diagnostics-extension-overview.md) and sent to the [Azure Monitor data sink](../agents/diagnostics-extension-overview.md#data-destinations), or the [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) on Linux machines, or the newer [Azure Monitor Agent](../agents/azure-monitor-agent-overview.md) (AMA) via data collection rules. Retention for these metrics is 93 days.
-	**Guest OS metrics collected by Log Analytics agent** - 31 days to 2 years. These are performance counters collected by the Log Analytics agent and sent to a Log Analytics workspace. Retention for these metrics is 31 days, and can be extended up to 2 years.

**Application Insights log-based metrics**. varies. - Behind the scene, [log-based metrics](../app/pre-aggregated-metrics-log-metrics.md) translate into log queries. Their retention matches the retention of events in underlying logs (31 days to 2 years). For Application Insights resources, logs are stored for 90 days.


> [!NOTE]
> You can [send platform metrics for Azure Monitor resources to a Log Analytics workspace](./resource-logs.md#send-to-azure-storage) for long term trending.





## Next steps

- Learn more about the [Azure Monitor data platform](../data-platform.md).
- Learn about [log data in Azure Monitor](../logs/data-platform-logs.md).
- Learn about the [monitoring data available](../agents/data-sources.md) for different resources in Azure.
