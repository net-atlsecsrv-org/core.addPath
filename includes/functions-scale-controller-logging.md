---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
---

| | |
|--|--|
|**`<DESTINATION>`**| The destination to which logs are sent. Valid values are `AppInsights` and `Blob`.<br/>When you use `AppInsights`, make sure [Application Insights is enabled in your function app](../articles/azure-functions/functions-monitoring.md#enable-application-insights-integration).<br/>When you set the destination to `Blob`, logs are created in a blob container named `azure-functions-scale-controller` in the default storage account set in the `AzureWebJobsStorage` application setting. |
|**`<VERBOSITY>`** | Specifies the level of logging. Supported values are `None`, `Warning`, and `Verbose`.<br/>When set to `Verbose`, the scale controller logs a reason for every change in the worker count, as well as information about the triggers that factor into those decisions. Verbose logs include trigger warnings and the hashes used by the triggers before and after the scale controller runs. |

> [!CAUTION]
> Don't leave scale controller logging enabled. Enable logging until you have collected enough data to understand how the scale controller is behaving, and then disable it.