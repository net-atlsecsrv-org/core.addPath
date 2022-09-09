---
title: Azure Monitor for SAP Solutions overview and architecture| Microsoft Docs
description: This article provides answers to frequently asked questions about Azure monitor for SAP solutions
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn

---

# Azure monitor for SAP solutions (preview)

## Overview

Azure Monitor for SAP Solutions is an Azure-native monitoring product for customers, running their SAP landscapes on Azure. The product works with both [SAP on Azure Virtual Machines](./hana-get-started.md) and [SAP on Azure Large Instances](./hana-overview-architecture.md).
With Azure Monitor for SAP Solutions, customers can collect telemetry data from Azure infrastructure and databases in one central location and visually correlate telemetry data for faster troubleshooting.

Azure Monitor for SAP Solutions is offered through Azure Marketplace. It provides a simple, intuitive setup experience and takes only a few clicks to deploy the resource for Azure Monitor for SAP Solutions (known as **SAP monitor resource**).

Customers can monitor different components of an SAP landscape such as Azure Virtual Machines, High-availability cluster, SAP HANA database and so on, by adding the corresponding **provider** for that component.

Supported infrastructure:

- Azure Virtual Machine
- Azure Large Instance

Supported databases:
- SAP HANA Database
- Microsoft SQL server

Azure Monitor for SAP Solutions leverages the power of existing [Azure Monitor](../../../azure-monitor/overview.md) capabilities such as Log Analytics and [Workbooks](../../../azure-monitor/platform/workbooks-overview.md) to provide additional monitoring capabilities. Customers can create [custom visualizations](../../../azure-monitor/platform/workbooks-overview.md#getting-started) by editing the default Workbooks provided by Azure Monitor for SAP Solutions, write [custom queries](../../../azure-monitor/log-query/log-analytics-tutorial.md) and create [custom alerts](../../../azure-monitor/learn/tutorial-response.md) by using Azure Log Analytics workspace, take advantage of [flexible retention period](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) and connect monitoring data with their ticketing system.

## What data does Azure Monitor for SAP solutions collect?

Data collection in Azure Monitor for SAP Solutions depends on the providers that are configured by customers. During Public Preview, the following data is being collected.

High-availability Pacemaker cluster telemetry:
- Node, resource, and SBD device status
- Pacemaker location constraints
- Quorum votes and ring status
- [Others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetry:
- CPU, memory, disk, and network utilization
- HANA System Replication (HSR)
- HANA backup
- HANA host status
- Index server and Name server roles

Microsoft SQL server telemetry:
- CPU, memory, disk utilization
- Hostname, SQL Instance name, SAP System ID
- Batch Requests, Compilations, and page Life Expectancy over time
- Top 10 most expensive SQL statements over time
- Top 12 largest table in the SAP system
- Problems recorded in the SQL Server Error logs
- Blocking processes and SQL Wait Statistics over time

## Data sharing with Microsoft

Azure Monitor for SAP Solutions collects system metadata to provide improved support for our SAP on Azure customers. No PII/EUII is collected.
Customers can enable data sharing with Microsoft at the time of creating Azure Monitor for SAP Solutions resource by choosing *Share* from the drop-down.
It is highly recommended that customers enable data sharing, as it gives Microsoft support and engineering teams more information about customer environment and provides improved support to our mission-critical SAP on Azure customers.

## Architecture overview

At a high level, the following diagram explains how Azure Monitor for SAP Solutions collects telemetry from SAP HANA database. The architecture is agnostic to whether SAP HANA is deployed on Azure Virtual Machines or Azure Large Instances.

![Azure Monitor for SAP solutions architecture](./media/azure-monitor-sap/azure-monitor-architecture.png)

The key components of the architecture are:
- Azure portal – the starting point for customers. Customers can navigate to marketplace within Azure portal and discover Azure Monitor for SAP Solutions
- Azure Monitor for SAP Solutions resource – a landing place for customers to view monitoring telemetry
- Managed resource group – deployed automatically as part of the Azure Monitor for SAP Solutions resource deployment. The resources deployed within managed resource group help in collection of telemetry. Key resources deployed and their purpose are:
   - Azure Virtual Machine: Also known as *collector VM*. This is a Standard_B2ms VM. The main purpose of this VM is to host the *Monitoring Payload*. Monitoring payload refers to the logic of collecting telemetry from the source systems and transferring the collected data to the monitoring framework. In the above diagram, the monitoring payload contains the logic to connect to SAP HANA database over SQL port.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): This resource is deployed to securely hold SAP HANA database credentials and to store information about [providers](./azure-monitor-providers.md).
   - Log Analytics Workspace: the destination where the telemetry data resides.
      - Visualization is built on top of telemetry in Log Analytics using [Azure Workbooks](../../../azure-monitor/platform/workbooks-overview.md). Customers can customize visualization. Customers can also pin their Workbooks or specific visualization within Workbooks to Azure dashboard for autorefresh capability with lowest granularity of 30 minutes.
      - Customers can use their existing workspace within the same subscription as SAP monitor resource by choosing this option at the time of deployment.
      - Customers can use Kusto query language (KQL) to run [queries](../../../azure-monitor/log-query/log-query-overview.md) against the raw tables inside Log Analytics workspace. Look at *Custom Logs*.

> [!Note]
> Customers are responsible for patching and maintaining the VM, deployed in the managed resource group.

> [!Tip]
> Customers can choose to use an existing Log Analytics workspace for telemetry collection, if it is deployed within the same Azure subscription as the resource for Azure Monitor for SAP Solutions.

### Architecture Highlights

Following are the key highlights of the architecture:
 - **Multi-instance** - Customers can create monitor for multiple instances of a given component type (for example, HANA DB, HA cluster, Microsoft SQL server) across multiple SAP SIDs within a VNET with a single resource of Azure Monitor for SAP Solutions.
 - **Multi-provider** - The above architecture diagram shows the SAP HANA provider as an example. Similarly, customers can configure additional providers for corresponding components (for example, HANA DB, HA cluster, Microsoft SQL server) to collect data from those components.
 - **Open source** - The source code of Azure Monitor for SAP Solutions is available in [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Customers can refer to the provider code and learn more about the product, contribute or share feedback.
 - **Extensible query framework** - SQL queries to collect telemetry data are written in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Additional SQL queries to collect more telemetry data can be easily added. Customers can request specific telemetry data to be added to Azure Monitor for SAP Solutions, by leaving feedback through link in the end of this document or contacting their account team.

## Pricing
Azure Monitor for SAP Solutions is a free product (no license fee). Customers are responsible for paying the cost for the underlying components in the managed resource group.

## Next steps

Learn about providers and create your first Azure Monitor for SAP Solutions resource.
 - Learn more about [Providers](./azure-monitor-providers.md)
 - [Deploy Azure Monitor for SAP solutions with Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Do you have questions about Azure Monitor for SAP Solutions? Check the [FAQ](./azure-monitor-faq.md) section