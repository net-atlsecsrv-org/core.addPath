---
title: Azure Status Monitor v2 overview | Microsoft Docs
description: An overview of Status Monitor v2. Monitor website performance without redeploying the website. Works with ASP.NET web apps hosted on-premises, in VMs, or on Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
---
# Status Monitor v2

Status Monitor v2 is a PowerShell module published to the [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
It replaces [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
The module provides codeless instrumentation of .NET web apps hosted with IIS.
Telemetry is sent to the Azure portal, where you can [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) your app.

## PowerShell Gallery

Status Monitor v2 is located here: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## Instructions
- See the [getting started instructions](status-monitor-v2-get-started.md) to get a start with concise code samples.
- See the [detailed instructions](status-monitor-v2-detailed-instructions.md) for a deep dive on how to get started.

## PowerShell API reference
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## Troubleshooting
- [Troubleshooting](status-monitor-v2-troubleshoot.md)
- [Known issues](status-monitor-v2-troubleshoot.md#known-issues)


## FAQ

- Does Status Monitor v2 support proxy installations?

  *Yes*. There are multiple ways to download Status Monitor v2. 
If your computer has internet access, you can onboard to the PowerShell Gallery by using `-Proxy` parameters.
You can also manually download the module and either install it on your computer or use it directly.
Each of these options is described in the [detailed instructions](status-monitor-v2-detailed-instructions.md).

- Does Status Monitor v2 support ASP.NET Core applications?

  *No*. For instructions to enable monitoring of ASP.NET Core applications, see [Application Insights for ASP.NET Core applications](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). There's no need to install StatusMonitor for an ASP.NET Core application. This is true even if ASP.NET Core application is hosted in IIS.
  
Does Status Monitor v2 support ASP.NET Core applications? 

  *No*. Please follow [these](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) instructions to enable monitoring for ASP.NET Core Applications. There is no need to install StatusMonitor for an ASP.NET Core Application. This is true even if ASP.NET Core Application is hosted in IIS.

- How do I verify that the enablement succeeded?

  - The [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet can be used to verify that enablement succeeded.
  - We recommend you use [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) to quickly determine if your app is sending telemetry.

  - You can also use [Log Analytics](../log-query/get-started-portal.md) to list all the cloud roles currently sending telemetry:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## Next steps

View your telemetry:

* [Explore metrics](../../azure-monitor/app/metrics-explorer.md) to monitor performance and usage.
* [Search events and logs](../../azure-monitor/app/diagnostic-search.md) to diagnose problems.
* [Use Analytics](../../azure-monitor/app/analytics.md) for more advanced queries.
* [Create dashboards](../../azure-monitor/app/overview-dashboard.md).

Add more telemetry:

* [Create web tests](monitor-web-app-availability.md) to make sure your site stays live.
* [Add web client telemetry](../../azure-monitor/app/javascript.md) to see exceptions from web page code and to enable trace calls.
* [Add the Application Insights SDK to your code](../../azure-monitor/app/asp-net.md) so you can insert trace and log calls.

