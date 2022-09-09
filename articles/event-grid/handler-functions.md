---
title: Azure function as an event handler for Azure Event Grid events
description: Describes how you can use Azure functions as event handlers for Event Grid events. 
ms.topic: conceptual
ms.date: 09/18/2020
---

# Azure function as an event handler for Event Grid events

An event handler is the place where the event is sent. The handler takes an action to process the event. Several Azure services are automatically configured to handle events and **Azure Functions** is one of them. 


To use an Azure function as a handler for events, follow one of these approaches: 

-	Use [Event Grid trigger](../azure-functions/functions-bindings-event-grid-trigger.md).  Specify **Azure Function** as the **endpoint type**. Then, specify the Azure function app and the function that will handle events. 
-	Use [HTTP trigger](../azure-functions/functions-bindings-http-webhook.md).  Specify **Web Hook** as the **endpoint type**. Then, specify the URL for the Azure function that will handle events. 

We recommend that you use the first approach (Event Grid trigger) as it has the following advantages over the second approach:
-	Event Grid automatically validates Event Grid triggers. With generic HTTP triggers, you must implement the [validation response](webhook-event-delivery.md) yourself.
-	Event Grid automatically adjusts the rate at which events are delivered to a function triggered by an Event Grid event based on the perceived rate at which the function can process events. This rate match feature averts delivery errors that stem from the inability of a function to process events as the function’s event processing rate can vary over time. To improve efficiency at high throughput, enable batching on the event subscription. For more information, see [Enable batching](#enable-batching).

    > [!NOTE]
    > Currently, you can't use an Event Grid trigger for an Azure Functions app when the event is delivered in the **CloudEvents** schema. Instead, use an HTTP trigger.

## Tutorials

|Title  |Description  |
|---------|---------|
| [Quickstart: Handle events with function](custom-event-to-function.md) | Sends a custom event to a function for processing. |
| [Tutorial: automate resizing uploaded images using Event Grid](resize-images-on-storage-blob-upload-event.md) | Users upload images through web app to storage account. When a storage blob is created, Event Grid sends an event to the function app, which resizes the uploaded image. |
| [Tutorial: stream big data into a data warehouse](event-grid-event-hubs-integration.md) | When Event Hubs creates a Capture file, Event Grid sends an event to a function app. The app retrieves the Capture file and migrates data to a data warehouse. |
| [Tutorial: Azure Service Bus to Azure Event Grid integration examples](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid sends messages from Service Bus topic to a function app and a logic app. |

## REST example (for PUT)

```json
{
	"properties": 
	{
		"destination": 
		{
			"endpointType": "AzureFunction",
			"properties": 
			{
				"resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
				"maxEventsPerBatch": 10,
				"preferredBatchSizeInKilobytes": 6400
			}
		},
		"eventDeliverySchema": "EventGridSchema"
	}
}
```

## Enable batching
For a higher throughput, enable batching on the subscription. If you are using the Azure portal, you can set maximum events per batch and the preferred batch size in kilo bytes at the time of creating a subscription or after the creation. 

You can configure batch settings using the Azure portal, PowerShell, CLI, or Resource Manager template. 

### Azure portal
At the time creating a subscription in the UI, on the **Create Event Subscription** page, switch to the **Advanced Features** tab, and set values for **Max events per batch** and **Preferred batch size in kilobytes**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Enable batching at the time of creating a subscription":::

You can update these values for an existing subscription on the **Features** tab of the **Event Grid Topic** page. 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Enable batching after creation":::

### Azure Resource Manager template
You can set **maxEventsPerBatch** and **preferredBatchSizeInKilobytes** in an Azure Resource Manager template. For more information, see [Microsoft.EventGrid eventSubscriptions template reference](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### Azure CLI
You can use the [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) or [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) command to configure batch-related settings using the following parameters: `--max-events-per-batch` or `--preferred-batch-size-in-kilobytes`.

### Azure PowerShell
You can use the [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) or [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) cmdlet to configure batch-related settings using the following parameters: `-MaxEventsPerBatch` or `-PreferredBatchSizeInKiloBytes`.

## Next steps
See the [Event handlers](event-handlers.md) article for a list of supported event handlers.