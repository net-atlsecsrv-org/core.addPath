---
title: Log alerts from Azure Monitor for containers | Microsoft Docs
description: This article describes how to create custom log alerts for memory and CPU utilization from Azure Monitor for containers.
ms.topic: conceptual
ms.date: 01/07/2020

---

# How to create log alerts from Azure Monitor for containers

Azure Monitor for containers monitors the performance of container workloads that are deployed to managed or self-managed Kubernetes clusters. To alert on what matters, this article describes how to create log-based alerts for the following situations with AKS clusters:

- When CPU or memory utilization on cluster nodes exceeds a threshold
- When CPU or memory utilization on any container within a controller exceeds a threshold as compared to a limit that's set on the corresponding resource
- *NotReady* status node counts
- *Failed*, *Pending*, *Unknown*, *Running*, or *Succeeded* pod-phase counts
- When free disk space on cluster nodes exceeds a threshold

To alert for high CPU or memory utilization, or low free disk space on cluster nodes, use the queries that are provided to create a metric alert or a metric measurement alert. While metric alerts have lower latency than log alerts, log alerts provide advanced querying and greater sophistication. Log alert queries compare a datetime to the present by using the *now* operator and going back one hour. (Azure Monitor for containers stores all dates in Coordinated Universal Time (UTC) format.)

If you're not familiar with Azure Monitor alerts, see [Overview of alerts in Microsoft Azure](../platform/alerts-overview.md) before you start. To learn more about alerts that use log queries, see [Log alerts in Azure Monitor](../platform/alerts-unified-log.md). For more about metric alerts, see [Metric alerts in Azure Monitor](../platform/alerts-metric-overview.md).

## Resource utilization log search queries

The queries in this section support each alerting scenario. They're used in step 7 of the [create alert](#create-an-alert-rule) section of this article.

The following query calculates average CPU utilization as an average of member nodes' CPU utilization every minute.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

The following query calculates average memory utilization as an average of member nodes' memory utilization every minute.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>The following queries use the placeholder values \<your-cluster-name> and \<your-controller-name> to represent your cluster and controller. Replace them with values specific to your environment when you set up alerts.

The following query calculates the average CPU utilization of all containers in a controller as an average of CPU utilization of every container instance in a controller every minute. The measurement is a percentage of the limit set up for a container.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

The following query calculates the average memory utilization of all containers in a controller as an average of memory utilization of every container instance in a controller every minute. The measurement is a percentage of the limit set up for a container.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

The following query returns all nodes and counts that have a status of *Ready* and *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
The following query returns pod phase counts based on all phases: *Failed*, *Pending*, *Unknown*, *Running*, or *Succeeded*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterId
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>To alert on certain pod phases, such as *Pending*, *Failed*, or *Unknown*, modify the last line of the query. For example, to alert on *FailedCount* use: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

The following query returns cluster nodes disks which exceed 90% free space used. To get the cluster ID, first run the following query and copy the value from the `ClusterId` property:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## Create an alert rule

This section walks through the creation of a metric measurement alert rule using performance data from Azure Monitor for containers. You can use this basic process with a variety of log queries to alert on different performance counters. Use one of the log search queries provided earlier to start with. To create using an ARM template, see [Samples of Log alert creation using Azure Resource Template](../platform/alerts-log-create-templates.md).

>[!NOTE]
>The following procedure to create an alert rule for container resource utilization requires you to switch to a new log alerts API as described in [Switch API preference for log alerts](../platform/alerts-log-api-switch.md).
>

1. Sign in to the [Azure portal](https://portal.azure.com).
2. In the Azure portal, search for and select **Log Analytics workspaces**.
3. In your list of Log Analytics workspaces, select the workspace supporting Azure Monitor for containers. 
4. In the pane on the left side, select **Logs** to open the Azure Monitor logs page. You use this page to write and execute Azure log queries.
5. On the **Logs** page, paste one of the [queries](#resource-utilization-log-search-queries) provided earlier into the **Search query** field and then select **Run** to validate the results. If you do not perform this step, the **+New alert** option is not available to select.
6. Select **+New alert** to create a log alert.
7. In the **Condition** section, select the **Whenever the Custom log search is \<logic undefined>** pre-defined custom log condition. The **custom log search** signal type is automatically selected because we're creating an alert rule directly from the Azure Monitor logs page.  
8. Paste one of the [queries](#resource-utilization-log-search-queries) provided earlier into the **Search query** field.
9. Configure the alert as follows:

    1. From the **Based on** drop-down list, select **Metric measurement**. A metric measurement creates an alert for each object in the query that has a value above our specified threshold.
    1. For **Condition**, select **Greater than**, and enter **75** as an initial baseline **Threshold** for the CPU and memory utilization alerts. For the low disk space alert, enter **90**. Or enter a different value that meets your criteria.
    1. In the **Trigger Alert Based On** section, select **Consecutive breaches**. From the drop-down list, select **Greater than**, and enter **2**.
    1. To configure an alert for container CPU or memory utilization, under **Aggregate on**, select **ContainerName**. To configure for cluster node low disk alert, select **ClusterId**.
    1. In the **Evaluated based on** section, set the **Period** value to **60 minutes**. The rule will run every 5 minutes and return records that were created within the last hour from the current time. Setting the time period to a wide window accounts for potential data latency. It also ensures that the query returns data to avoid a false negative in which the alert never fires.

10. Select **Done** to complete the alert rule.
11. Enter a name in the **Alert rule name** field. Specify a **Description** that provides details about the alert. And select an appropriate severity level from the options provided.
12. To immediately activate the alert rule, accept the default value for **Enable rule upon creation**.
13. Select an existing **Action Group** or create a new group. This step ensures that the same actions are taken every time that an alert is triggered. Configure based on how your IT or DevOps operations team  manages incidents.
14. Select **Create alert rule** to complete the alert rule. It starts running immediately.

## Next steps

- View [log query examples](container-insights-log-search.md#search-logs-to-analyze-data) to see pre-defined queries and examples to evaluate or customize for alerting, visualizing, or analyzing your clusters.

- To learn more about Azure Monitor and how to monitor other aspects of your Kubernetes cluster, see [View Kubernetes cluster performance](container-insights-analyze.md) and [View Kubernetes cluster health](./container-insights-overview.md).