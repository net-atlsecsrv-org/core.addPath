---
title: Monitor cluster performance - Azure HDInsight 
description: How to monitor health and performance of Apache Hadoop clusters in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
---

# Monitor cluster performance in Azure HDInsight

Monitoring the health and performance of an HDInsight cluster is essential for maintaining optimal performance and resource utilization. Monitoring can also help you detect and address cluster configuration errors and user code issues.

The following sections describe how to monitor and optimize the load on your clusters, Apache Hadoop YARN queues and detect storage throttling issues.

## Monitor cluster load

Hadoop clusters can deliver the most optimal performance when the load on cluster is evenly distributed across all the nodes. This enables the processing tasks to run without being constrained by RAM, CPU, or disk resources on individual nodes.

To get a high-level look at the nodes of your cluster and their loading, sign in to the [Ambari Web UI](hdinsight-hadoop-manage-ambari.md), then select the **Hosts** tab. Your hosts are listed by their fully qualified domain names. Each host's operating status is shown by a colored health indicator:

| Color | Description |
| --- | --- |
| Red | At least one master component on the host is down. Hover to see a tooltip that lists affected components. |
| Orange | At least one secondary component on the host is down. Hover to see a tooltip that lists affected components. |
| Yellow | Ambari Server has not received a heartbeat from the host for more than 3 minutes. |
| Green | Normal running state. |

You'll also see columns showing the number of cores and amount of RAM for each host, and the disk usage and load average.

![Apache Ambari hosts tab overview](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Select any of the host names for a detailed look at components running on that host and their metrics. The metrics are shown as a selectable timeline of CPU usage, load, disk usage, memory usage, network usage, and numbers of processes.

![Apache Ambari host details overview](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

See [Manage HDInsight clusters by using the Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) for details on setting alerts and viewing metrics.

## YARN queue configuration

Hadoop has various services running across its distributed platform. YARN (Yet Another Resource Negotiator) coordinates these services and allocates cluster resources to ensure that any load is evenly distributed across the cluster.

YARN divides the two responsibilities of the JobTracker, resource management and job scheduling/monitoring, into two daemons: a global Resource Manager, and a per-application ApplicationMaster (AM).

The Resource Manager is a *pure scheduler*, and solely arbitrates available resources between all competing applications. The Resource Manager ensures that all resources are always in use, optimizing for various constants such as SLAs, capacity guarantees, and so forth. The ApplicationMaster negotiates resources from the Resource Manager, and works with the NodeManager(s) to execute and monitor the containers and their resource consumption.

When multiple tenants share a large cluster, there is competition for the cluster's resources. The CapacityScheduler is a pluggable scheduler that assists in resource sharing by queueing up requests. The CapacityScheduler also supports *hierarchical queues* to ensure that resources are shared between the sub-queues of an organization, before other applications' queues are allowed to use free resources.

YARN allows us to allocate resources to these queues, and shows you whether all of your available resources are assigned. To view information about your queues, sign in to the Ambari Web UI, then select **YARN Queue Manager** from the top menu.

![Apache Ambari YARN Queue Manager](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

The YARN Queue Manager page shows a list of your queues on the left, along with the percentage of capacity assigned to each.

![YARN Queue Manager details page](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

For a more detailed look at your queues, from the Ambari dashboard, select the **YARN** service from the list on the left. Then under the **Quick Links** dropdown menu, select **Resource Manager UI** underneath your active node.

![Resource Manager UI menu links](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

In the Resource Manager UI, select **Scheduler** from the left-hand menu. You see a list of your queues underneath *Application Queues*. Here you can see the capacity used for each of your queues, how well the jobs are distributed between them, and whether any jobs are resource-constrained.

![Apache HAdoop Resource Manager UI menu](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## Storage throttling

A cluster's performance bottleneck can happen at the storage level. This type of bottleneck is most often due to *blocking* input/output (IO) operations, which happen when your running tasks send more IO than the storage service can handle. This blocking creates a queue of IO requests waiting to be processed until after current IOs are processed. The blocks are due to *storage throttling*, which is not a physical limit, but rather a limit imposed by the storage service by a service level agreement (SLA). This limit ensures that no single client or tenant can monopolize the service. The SLA limits the number of IOs per second (IOPS) for Azure Storage - for details, see [Azure Storage Scalability and Performance Targets](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

If you are using Azure Storage, for information on monitoring storage-related issues, including throttling, see [Monitor, diagnose, and troubleshoot Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

If your cluster's backing store is Azure Data Lake Storage (ADLS), your throttling is most likely due to bandwidth limits. Throttling in this case could be identified by observing throttling errors in task logs. For ADLS, see the throttling section for the appropriate service in these articles:

* [Performance tuning guidance for Apache Hive on HDInsight and Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Performance tuning guidance for MapReduce on HDInsight and Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Performance tuning guidance for Apache Storm on HDInsight and Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## Next steps

Visit the following links for more information about troubleshooting and monitoring your clusters:

* [Analyze HDInsight logs](hdinsight-debug-jobs.md)
* [Debug apps with Apache Hadoop YARN logs](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Enable heap dumps for Apache Hadoop services on Linux-based HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
