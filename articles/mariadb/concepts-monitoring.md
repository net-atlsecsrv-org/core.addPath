---
title: Monitoring in Azure Database for MariaDB
description: This article describes the metrics for monitoring and alerting for Azure Database for MariaDB, including CPU, storage, and connection statistics.
author: andrela
ms.author: ajlam
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
---
# Monitoring in Azure Database for MariaDB
Monitoring data about your servers helps you troubleshoot and optimize for your workload. Azure Database for MariaDB provides various metrics that give insight into the behavior of your server.

## Metrics
All Azure metrics have a one-minute frequency, and each metric provides 30 days of history. You can configure alerts on the metrics. Other tasks include setting up automated actions, performing advanced analytics, and archiving history. For more information, see the [Azure Metrics Overview](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

For step by step guidance, see [How to set up alerts](howto-alert-metric.md).

### List of metrics
These metrics are available for Azure Database for MariaDB:

|Metric|Metric Display Name|Unit|Description|
|---|---|---|---|
|cpu_percent|CPU percent|Percent|The percentage of CPU in use.|
|memory_percent|Memory percent|Percent|The percentage of memory in use.|
|io_consumption_percent|IO percent|Percent|The percentage of IO in use.|
|storage_percent|Storage percentage|Percent|The percentage of storage used out of the server's maximum.|
|storage_used|Storage used|Bytes|The amount of storage in use. The storage used by the service may include the database files, transaction logs, and the server logs.|
|serverlog_storage_percent|Server Log storage percent|Percent|The percentage of server log storage used out of the server's maximum server log storage.|
|serverlog_storage_usage|Server Log storage used|Bytes|The amount of server log storage in use.|
|serverlog_storage_limit|Server Log storage limit|Bytes|The maximum server log storage for this server.|
|storage_limit|Storage limit|Bytes|The maximum storage for this server.|
|active_connections|Active Connections|Count|The number of active connections to the server.|
|connections_failed|Failed Connections|Count|The number of failed connections to the server.|
|network_bytes_egress|Network Out|Bytes|Network Out across active connections.|
|network_bytes_ingress|Network In|Bytes|Network In across active connections.|

## Server logs

You can enable slow query logging on your server. These logs are also available through Azure Diagnostic Logs in Azure Monitor logs, Event Hubs, and Storage Account. To learn more about logging, visit the [server logs](concepts-server-logs.md) page.

## Query Store

[Query Store](concepts-query-store.md) is a public preview feature that keeps track of query performance over time including query runtime statistics and wait events. The feature persists query runtime performance information in the **mysql** schema. You can control the collection and storage of data via various configuration knobs.

## Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) works in conjunction with Query Store to provide visualizations accessible from the Azure portal. These charts enable you to identify key queries that impact performance. Query Performance Insight is in public preview and is accessible in the **Intelligent Performance** section of your Azure Database for MariaDB server's portal page.

## Performance Recommendations

The [Performance Recommendations](concepts-performance-recommendations.md) feature identifies opportunities to improve workload performance. The public preview release of Performance Recommendations provides you with recommendations for creating new indexes that have the potential to improve the performance of your workloads. To produce index recommendations, the feature takes into consideration various database characteristics, including its schema and the workload as reported by Query Store. After implementing any performance recommendation, customers should test performance to evaluate the impact of those changes.

## Next steps

- For more information on how to access and export metrics using the Azure portal, REST API, or CLI, see the [Azure Metrics Overview](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - See [How to set up alerts](howto-alert-metric.md) for guidance on creating an alert on a metric.
