---
# Mandatory fields.
title: Set up diagnostics
titleSuffix: Azure Digital Twins
description: See how to enable logging with diagnostics settings.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
---

# Troubleshooting Azure Digital Twins: Diagnostics logging

Azure Digital Twins collects [metrics](troubleshoot-metrics.md) for your service instance that give information about the state of your resources. You can use these metrics to assess the overall health of Azure Digital Twins service and the resources connected to it. These user-facing statistics help you see what is going on with your Azure Digital Twins and help perform root-cause analysis on issues without needing to contact Azure support.

This article shows you how to turn on **diagnostics logging** for your metrics data from your Azure Digital Twins instance. You can use these logs to help you troubleshoot service issues, and configure diagnostic settings to send Azure Digital Twins metrics to different destinations. You can read more about these settings in [*Create diagnostic settings to send platform logs and metrics to different destinations*](../azure-monitor/platform/diagnostic-settings.md).

## Turn on diagnostic settings with the Azure portal

Here is how to enable diagnostic settings for your Azure Digital Twins instance:

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to your Azure Digital Twins instance. You can find it by typing its name into the portal search bar. 

2. Select **Diagnostic settings** from the menu, then **Add diagnostic setting**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot showing the diagnostic settings page and button to add":::

3. On the page that follows, fill in the following values:
     * **Diagnostic setting name**: Give the diagnostic settings a name.
     * **Category details**: Choose which operations you want to monitor, and check the boxes to enable diagnostics for those operations. The operations that diagnostic settings can report on are:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        For more details about these options, see the [*Category details*](#category-details) section below.
     * **Destination details**: Choose where you want to send the logs. You can select any combination of the three options:
        - Send to Log Analytics
        - Archive to a storage account
        - Stream to an event hub

        You may be asked to fill in additional details if they are necessary for your destination selection.  
    
4. Save the new settings. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot showing the diagnostic setting page where the user has filled in a diagnostic setting name, and made some checkbox selections for Category details and Destination details. The Save button is highlighted.":::

New settings take effect in about 10 minutes. After that, logs appear in the configured target back on the **Diagnostic settings** page for your instance. 

## Category details

Here are more details about the log categories that can be selected under **Category details** when setting up diagnostic settings.

| Log category | Description |
| --- | --- |
| ADTModelsOperation | Log all API calls pertaining to Models |
| ADTQueryOperation | Log all API calls pertaining to Queries |
| ADTEventRoutesOperation | Log all API calls pertaining to Event Routes as well as egress of events from Azure Digital Twins to an endpoint service like Event Grid, Event Hubs and Service Bus |
| ADTDigitalTwinsOperation | Log all API calls pertaining to Azure Digital Twins |

Each log category consists of operations of write, read, delete, and action.  These map to REST API calls as follows:

| Event type | REST API operations |
| --- | --- |
| Write | PUT and PATCH |
| Read | GET |
| Delete | DELETE |
| Action | POST |

Here is a comprehensive list of the operations and corresponding [Azure Digital Twins REST API calls](https://docs.microsoft.com/rest/api/azure-digitaltwins/) that are logged in each category. 

>[!NOTE]
> Each log category contains several operations/REST API calls. In the table below, each log category maps to all operations/REST API calls underneath it until the next log category is listed. 

| Log category | Operation | REST API calls and other events |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | Digital Twin Models Update API |
|  | Microsoft.DigitalTwins/models/read | Digital Twin Models Get By ID and List APIs |
|  | Microsoft.DigitalTwins/models/delete | Digital Twin Models Delete API |
|  | Microsoft.DigitalTwins/models/action | Digital Twin Models Add API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | Query Twins API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | Event Routes Add API |
|  | Microsoft.DigitalTwins/eventroutes/read | Event Routes Get By ID and List APIs |
|  | Microsoft.DigitalTwins/eventroutes/delete | Event Routes Delete API |
|  | Microsoft.DigitalTwins/eventroutes/action | Failure while attempting to publish events to an endpoint service (not an API call) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins Add, Add Relationship, Update, Update Component |
|  | Microsoft.DigitalTwins/digitaltwins/read | Digital Twins Get By ID, Get Component, Get Relationship by ID, List Incoming Relationships, List Relationships |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins Delete, Delete Relationship |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins Send Component Telemetry, Send Telemetry |

## Log schemas 

Each log category has a schema that defines how events in that category are reported. Each individual log entry is stored as text and formatted as a JSON blob. The fields in the log and example JSON bodies are provided for each log type below. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation`, and `ADTQueryOperation` use a consistent API log schema; `ADTEventRoutesOperation` has its own separate schema.

### API log schemas

This log schema is consistent for `ADTDigitalTwinsOperation`, `ADTModelsOperation`, and `ADTQueryOperation`. It contains information pertinent to API calls to an Azure Digital Twins instance.

Here are the field and property descriptions for API logs.

| Field name | Data type | Description |
|-----|------|-------------|
| `Time` | DateTime | The date and time that this event occurred, in UTC |
| `ResourceID` | String | The Azure Resource Manager Resource ID for the resource where the event took place |
| `OperationName` | String  | The type of action being performed during the event |
| `OperationVersion` | String | The API Version utilized during the event |
| `Category` | String | The type of resource being emitted |
| `ResultType` | String | Outcome of the event |
| `ResultSignature` | String | Http status code for the event |
| `ResultDescription` | String | Additional details about the event |
| `DurationMs` | String | How long it took to perform the event in milliseconds |
| `CallerIpAddress` | String | A masked source IP address for the event |
| `CorrelationId` | Guid | Customer provided unique identifier for the event |
| `Level` | String | The logging severity of the event |
| `Location` | String | The region where the event took place |
| `RequestUri` | Uri | The endpoint utilized during the event |

Below are example JSON bodies for these types of logs.

#### ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### Egress log schemas

This is the schema for `ADTEventRoutesOperation` logs. These contain details pertaining to exceptions and the API operations around egress endpoints connected to an Azure Digital Twins instance.

|Field name | Data type | Description |
|-----|------|-------------|
| `Time` | DateTime | The date and time that this event occurred, in UTC |
| `ResourceId` | String | The Azure Resource Manager Resource ID for the resource where the event took place |
| `OperationName` | String  | The type of action being performed during the event |
| `Category` | String | The type of resource being emitted |
| `ResultDescription` | String | Additional details about the event |
| `Level` | String | The logging severity of the event |
| `Location` | String | The region where the event took place |
| `EndpointName` | String | The name of egress endpoint created in Azure Digital Twins |

Below are example JSON bodies for these types of logs.

#### ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## Next steps

* For more information about configuring diagnostics, see [*Collect and consume log data from your Azure resources*](../azure-monitor/platform/platform-logs-overview.md).
* For information about the Azure Digital Twins metrics, see [*Troubleshooting: View metrics with Azure Monitor*](troubleshoot-metrics.md).
* To see how to enable alerts for your metrics, see [*Troubleshooting: Set up alerts*](troubleshoot-alerts.md).
