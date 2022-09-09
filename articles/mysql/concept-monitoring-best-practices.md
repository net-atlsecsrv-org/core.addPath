---
title: Monitoring best practices - Azure Database for MySQL
description: This article describes the best practices to monitor your Azure Database for MySQL.
author: mksuni 
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
---

# Best practices for monitoring Azure Database for MySQL -Single server

Learn about the best practices that can be used to monitor your database operations and ensure that the performance is not compromised as data size grows. As we add new capabilities to the platform, we will continue refine the best practices detailed in this section.

## Layout of the current monitoring toolkit

Azure Database for MySQL provides tools and methods you can use to monitor usage easily, add, or remove resources (such as CPU, memory, or I/O), troubleshoot potential problems, and help improve the performance of a database. You can [monitor performance metrics](concepts-monitoring.md#metrics) on a regular basis to see the average, maximum, and minimum values for a variety of time ranges.

You can [set up alerts](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) for a metric threshold, so you are informed if the server has reached those limits and take appropriate actions.  

Monitor the database server to make sure that the resources assigned to the database can handle the application workload. If the database is hitting resource limits, consider:
    * Identifying and optimizing the top resource-consuming queries. 
    * Adding more resources by upgrading the service tier.

### CPU utilization
Monitor CPU usage and if the database is exhausting CPU resources. If CPU usage is 90% or more than you should scale up your compute by increasing the number of vCores or scale to next pricing tier.  Make sure that the throughput or concurrency is as expected as you scale up/down the CPU. 

### Memory 
The amount of memory available for the database server is proportional to the [number of vCores](concepts-pricing-tiers.md). Make sure the memory is enough for the workload. Load test your application to verify the memory is sufficient for read and write operations. If the database memory consumption frequently grows beyond a defined threshold, this indicates that you should upgrade your instance by increasing vCores or higher performance tier. Use [Query Store](concepts-query-store.md), [Query Performance Recommendations](concepts-performance-recommendations.md) to identify queries with the longest duration, most executed. Explore opportunities to optimize. 

### Storage 
The [amount of storage](howto-create-manage-server-portal.md#scale-compute-and-storage) provisioned for the MySQL server determines the IOPs for your server. The storage used by the service includes the database files, transaction logs, the server logs and backup snapshots. Ensure that the consumed disk space does not constantly exceed above 85 percent of the total provisioned disk space. If that is the case, you need to delete or archive data from the database server to free up some space. 

### Network traffic 

**Network Receive Throughput, Network Transmit Throughput** – The rate of network traffic to and from the MySQL instance in megabytes per second. You need to evaluate the throughput requirement for server and constantly monitor the traffic if throughput is lower than expected. 

### Database connections 
**Database Connections** – The number of client sessions that are connected to the Azure Database for MySQL should be aligned with the [connection limits for the selected SKU](concepts-server-parameters.md#max_connections) size. 


## Next steps

- [Best practice for performance of Azure Database for MySQL](concept-performance-best-practices.md)
- [Best practice for server operations using Azure Database for MySQL](concept-operation-excellence-best-practices.md)
