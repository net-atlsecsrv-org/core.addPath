---
title: Azure Monitor for SAP Solutions providers| Microsoft Docs
description: This article provides answers to frequently asked questions about Azure monitor for SAP solutions
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''

ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows

ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch

---

# Azure monitor for SAP solutions providers (preview)

## Overview  

In the context of Azure Monitor for SAP Solutions, a *provider type* refers to a specific *provider*. For example *SAP HANA*, which is configured for a specific component within the SAP landscape, like SAP HANA database. A provider contains the connection information for the corresponding component and helps to collect telemetry data from that component. One Azure Monitor for SAP Solutions resource (also known as SAP monitor resource) can be configured with multiple providers of the same provider type or multiple providers of multiple provider types.
   
Customers can choose to configure different provider types to enable data collection from corresponding component in their SAP landscape. For Example, customers can configure one provider for SAP HANA provider type, another provider for High-availability cluster provider type and so on.  

Customers can also choose to configure multiple providers of a specific provider type to reuse the same SAP monitor resource and associated managed group. Lean more about managed resource group. 
For public preview, the following provider types are supported:   
- SAP HANA
- High-availability cluster
- Microsoft SQL Server

![Azure Monitor for SAP solutions providers](./media/azure-monitor-sap/azure-monitor-providers.png)

Customers are recommended to configure at least one provider from the available provider types at the time of deploying the SAP Monitor resource. By configuring a provider, customers initiate data collection from the corresponding component for which the provider is configured.   

If customers don't configure any providers at the time of deploying SAP monitor resource, although the SAP monitor resource will be successfully deployed, no telemetry data will be collected. Customers have an option to add providers after deployment through SAP monitor resource within Azure portal. Customers can add or delete providers from the SAP monitor resource at any time.

> [!Tip]
> If you would like Microsoft to implement a specific provider, please leave feedback through link at the end of this document or reach out your account team.  

## Provider type SAP HANA

Customers can configure one or more providers of provider type *SAP HANA* to enable data collection from SAP HANA database. The SAP HANA provider connects to the SAP HANA database over SQL port, pulls telemetry data from the database, and pushes it to the Log Analytics workspace in the customer subscription. The SAP HANA provider collects data every 1 minute from the SAP HANA database.  

In public preview, customers can expect to see the following data with SAP HANA provider: Underlying infrastructure utilization, SAP HANA Host status, SAP HANA System Replication, and SAP HANA Backup telemetry data. 
To configure SAP HANA provider, Host IP address, HANA SQL port number, and SYSTEMDB username and password are required. Customers are recommended to configure SAP HANA provider against SYSTEMDB, however additional providers can be configured against other database tenants.

![Azure Monitor for SAP solutions providers - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## Provider type High-availability cluster
Customers can configure one or more providers of provider type *High-availability cluster* to enable data collection from Pacemaker cluster within the SAP landscape. The High-availability cluster provider connects to Pacemaker,  using [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) endpoint, pulls telemetry data from the database and pushes it to Log Analytics workspace in the customer subscription. High-availability cluster provider collects data every 60 seconds from Pacemaker.  

In public preview, customers can expect to see the following data with High-availability cluster provider:   
 - Cluster status represented as roll-up of node and resource status 
 - [others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor for SAP solutions providers - High Availability cluster](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

To configure High-availability cluster provider, there are two primary steps involved: 
1. Install [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *each* node within Pacemaker cluster 
    - Customers can use Azure Automation scripts to deploy High-availability cluster. The scripts will install [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) on each cluster node.  
    - or customers can perform manual installation, following the steps on [this page](https://github.com/ClusterLabs/ha_cluster_exporter) 
2. Configure High-availability cluster provider in *each* node within Pacemaker cluster  
  To configure the High-availability cluster provider, Prometheus URL, Cluster name, Host name and System ID are needed.   
  Customers are recommended to configure one provider per cluster node.   

## Provider type Microsoft SQL server

Customers can configure one or more providers of provider type *Microsoft SQL Server* to enable data collection from [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server provider connects to Microsoft SQL Server over the SQL port, pulls telemetry data from the database, and pushes it to the Log Analytics workspace in the customer subscription. The SQL Server must be configured for SQL authentication and a SQL Server login, with the SAP DB as the default database for the provider, must be created. SQL Server provider collects data between every 60 seconds up to every hour from SQL server.  

In public preview, customers can expect to see the following data with SQL Server provider: underlying infrastructure utilization, top SQL statements, top largest table, problems recorded in the SQL Server error logs, blocking processes and others.  

To configure Microsoft SQL Server provider, the SAP System ID, the Host IP address, SQL Server port number as well as the SQL Server login name and password are required.

![Azure Monitor for SAP solutions providers - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## Next steps

- Create your first Azure Monitor for SAP solutions resource.
- Do you have questions about Azure Monitor for SAP Solutions? Check the [FAQ](./azure-monitor-faq.md) section
