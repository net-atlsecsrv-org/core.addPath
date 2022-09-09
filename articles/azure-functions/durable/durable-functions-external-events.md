---
title: Handling external events in Durable Functions - Azure
description: Learn how to handle external events in the Durable Functions extension for Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
---

# Handling external events in Durable Functions (Azure Functions)

Orchestrator functions have the ability to wait and listen for external events. This feature of [Durable Functions](durable-functions-overview.md) is often useful for handling human interaction or other external triggers.

> [!NOTE]
> External events are one-way asynchronous operations. They are not suitable for situations where the client sending the event needs a synchronous response from the orchestrator function.

## Wait for events

The [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET), `waitForExternalEvent` (JavaScript), and `wait_for_external_event` (Python) methods of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) allow an orchestrator function to asynchronously wait and listen for an external event. The listening orchestrator function declares the *name* of the event and the *shape of the data* it expects to receive.

# [C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

# [JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# [Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

The preceding example listens for a specific single event and takes action when it's received.

You can listen for multiple events concurrently, like in the following example, which waits for one of three possible event notifications.

# [C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

# [JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# [Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

The previous example listens for *any* of multiple events. It's also possible to wait for *all* events.

# [C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

In .NET, if the event payload cannot be converted into the expected type `T`, an exception is thrown.

# [JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# [Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` waits indefinitely for some input.  The function app can be safely unloaded while waiting. If and when an event arrives for this orchestration instance, it is awakened automatically and immediately processes the event.

> [!NOTE]
> If your function app uses the Consumption Plan, no billing charges are incurred while an orchestrator function is awaiting a task from `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript), or `wait_for_external_event` (Python), no matter how long it waits.

## Send events

You can use the [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) or `raiseEventAsync` (JavaScript) methods to send an external event to an orchestration. These methods are exposed by the [orchestration client](durable-functions-bindings.md#orchestration-client) binding. You can also use the built-in [raise event HTTP API](durable-functions-http-api.md#raise-event) to send an external event to an orchestration.

A raised event includes an *instance ID*, an *eventName*, and *eventData* as parameters. Orchestrator functions handle these events using the `WaitForExternalEvent` (.NET) or `waitForExternalEvent` (JavaScript) APIs. The *eventName* must match on both the sending and receiving ends in order for the event to be processed. The event data must also be JSON-serializable.

Internally, the "raise event" mechanisms enqueue a message that gets picked up by the waiting orchestrator function. If the instance is not waiting on the specified *event name,* the event message is added to an in-memory queue. If the orchestration instance later begins listening for that *event name,* it will check the queue for event messages.

> [!NOTE]
> If there is no orchestration instance with the specified *instance ID*, the event message is discarded.

Below is an example queue-triggered function that sends an "Approval" event to an orchestrator function instance. The orchestration instance ID comes from the body of the queue message.

# [C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

# [JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# [Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Internally, `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript), or `raise_event` (Python) enqueues a message that gets picked up by the waiting orchestrator function. If the instance is not waiting on the specified *event name,* the event message is added to an in-memory queue. If the orchestration instance later begins listening for that *event name,* it will check the queue for event messages.

> [!NOTE]
> If there is no orchestration instance with the specified *instance ID*, the event message is discarded.

### HTTP

The following is an example of an HTTP request that raises an "Approval" event to an orchestration instance. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

In this case, the instance ID is hardcoded as *MyInstanceId*.

## Next steps

> [!div class="nextstepaction"]
> [Learn how to implement error handling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Run a sample that waits for human interaction](durable-functions-phone-verification.md)