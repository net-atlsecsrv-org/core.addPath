---
title: Monitor applications running on Azure Functions with Application Insights - Azure Monitor | Microsoft Docs
description: Azure Monitor seamlessly integrates with your application running on Azure Functions, and allows you to monitor the performance and spot the problems with your apps in no time.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020

---

# Monitoring Azure Functions with Azure Monitor Application Insights

[Azure Functions](../../azure-functions/functions-overview.md) offers built-in integration with Azure Application Insights to monitor functions. 

Application Insights collects log, performance, and error data, and automatically detects performance anomalies. Application Insights includes powerful analytics tools to help you diagnose issues and to understand how your functions are used. When you have the visibility into your application data, you can continuously improve performance and usability. You can even use Application Insights during local function app project development. 

The required Application Insights instrumentation is built into Azure Functions. The only thing you need is a valid instrumentation key to connect your function app to an Application Insights resource. The instrumentation key should be added to your application settings when your function app resource is created in Azure. If your function app doesn't already have this key, you can set it manually. For more information read more about [monitoring Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## Distributed tracing for Java applications on Windows (public preview)

> [!IMPORTANT]
> This feature is currently in public preview for Java Azure Functions on Windows, distributed tracing for Java Azure Functions on Linux is not supported. 
> For Consumption plan it has a cold start of 8-9 seconds.

If your applications are written in Java you can view richer data from your functions applications, including, requests, dependencies, logs, and metrics. The additional data also lets you see and diagnose end-to-end transactions and see the application map, which aggregates many transactions to show a topological view of how the systems interact, and what the average performance and error rates are.

The end-to-end diagnostics and the application map provide visibility into one single transaction/request. Together these two features are very helpful for finding the root cause of reliability issues and performance bottlenecks on a per request basis.

### How to enable distributed tracing for Java Function apps?

Navigate to the functions app Overview blade, go to configurations. Under Application Settings, click "+ New application setting". Add the following two application settings with below values, then click Save on the upper left. DONE!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## Next Steps

* Read more instructions and information about monitoring [Monitoring Azure Functions](../../azure-functions/functions-monitoring.md)
* Get an overview of [Distributed Tracing](./distributed-tracing.md)
* See what [Application Map](./app-map.md?tabs=net) can do for your business
* Read about [requests and dependencies for Java apps](./java-in-process-agent.md)
* Learn more about [Azure Monitor](../overview.md) and [Application Insights](./app-insights-overview.md)
