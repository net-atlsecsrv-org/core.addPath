---
title: Timer trigger for Azure Functions
description: Understand how to use timer triggers in Azure Functions.
author: craigshoemaker

ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 11/18/2020
ms.author: cshoe
ms.custom: "devx-track-csharp, devx-track-python"

---
# Timer trigger for Azure Functions

This article explains how to work with timer triggers in Azure Functions. A timer trigger lets you run a function on a schedule.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

For information on how to manually run a timer-triggered function, see [Manually run a non HTTP-triggered function](./functions-manually-run-non-http.md).

## Packages - Functions 2.x and higher

The timer trigger is provided in the [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet package, version 3.x. Source code for the package is in the [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub repository.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## Packages - Functions 1.x

The timer trigger is provided in the [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet package, version 2.x. Source code for the package is in the [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub repository.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## Example

# [C#](#tab/csharp)

The following example shows a [C# function](functions-dotnet-class-library.md) that is executed each time the minutes have a value divisible by five (eg if the function starts at 18:57:00, the next performance will be at 19:00:00). The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# [C# Script](#tab/csharp-script)

The following example shows a timer trigger binding in a *function.json* file and a [C# script function](functions-reference-csharp.md) that uses the binding. The function writes a log indicating whether this function invocation is due to a missed schedule occurrence. The [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object is passed into the function.

Here's the binding data in the *function.json* file:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Here's the C# script code:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# [Java](#tab/java)

The following example function triggers and executes every five minutes. The `@TimerTrigger` annotation on the function defines the schedule using the same string format as [CRON expressions](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# [JavaScript](#tab/javascript)

The following example shows a timer trigger binding in a *function.json* file and a [JavaScript function](functions-reference-node.md) that uses the binding. The function writes a log indicating whether this function invocation is due to a missed schedule occurrence. A [timer object](#usage) is passed into the function.

Here's the binding data in the *function.json* file:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Here's the JavaScript code:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# [PowerShell](#tab/powershell)

The following example demonstrates how to configure the *function.json* and *run.ps1* file for a timer trigger in [PowerShell](./functions-reference-powershell.md).

```json
{
  "bindings": [
    {
      "name": "Timer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */5 * * * *"
    }
  ]
}
```

```powershell
# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format.
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' property is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"
```

An instance of the [timer object](#usage) is passed as the first argument to the function.

# [Python](#tab/python)

The following example uses a timer trigger binding whose configuration is described in the *function.json* file. The actual [Python function](functions-reference-python.md) that uses the binding is described in the *__init__.py* file. The object passed into the function is of type [azure.functions.TimerRequest object](/python/api/azure-functions/azure.functions.timerrequest). The function logic writes to the logs indicating whether the current invocation is due to a missed schedule occurrence.

Here's the binding data in the *function.json* file:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Here's the Python code:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

---

## Attributes and annotations

# [C#](#tab/csharp)

In [C# class libraries](functions-dotnet-class-library.md), use the [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

The attribute's constructor takes a CRON expression or a `TimeSpan`. You can use `TimeSpan` only if the function app is running on an App Service plan. `TimeSpan` is not supported for Consumption or Elastic Premium Functions.

The following example shows a CRON expression:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# [C# Script](#tab/csharp-script)

Attributes are not supported by C# Script.

# [Java](#tab/java)

The `@TimerTrigger` annotation on the function defines the schedule using the same string format as [CRON expressions](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

# [JavaScript](#tab/javascript)

Attributes are not supported by JavaScript.

# [PowerShell](#tab/powershell)

Attributes are not supported by PowerShell.

# [Python](#tab/python)

Attributes are not supported by Python.

---

## Configuration

The following table explains the binding configuration properties that you set in the *function.json* file and the `TimerTrigger` attribute.

|function.json property | Attribute property |Description|
|---------|---------|----------------------|
|**type** | n/a | Must be set to "timerTrigger". This property is set automatically when you create the trigger in the Azure portal.|
|**direction** | n/a | Must be set to "in". This property is set automatically when you create the trigger in the Azure portal. |
|**name** | n/a | The name of the variable that represents the timer object in function code. | 
|**schedule**|**ScheduleExpression**|A [CRON expression](#ncrontab-expressions) or a [TimeSpan](#timespan) value. A `TimeSpan` can be used only for a function app that runs on an App Service Plan. You can put the schedule expression in an app setting and set this property to the app setting name wrapped in **%** signs, as in this example: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|If `true`, the function is invoked when the runtime starts. For example, the runtime starts when the function app wakes up after going idle due to inactivity. when the function app restarts due to function changes, and when the function app scales out. So **runOnStartup** should rarely if ever be set to `true`, especially in production. |
|**useMonitor**|**UseMonitor**|Set to `true` or `false` to indicate whether the schedule should be monitored. Schedule monitoring persists schedule occurrences to aid in ensuring the schedule is maintained correctly even when function app instances restart. If not set explicitly, the default is `true` for schedules that have a recurrence interval greater than or equal to 1 minute. For schedules that trigger more than once per minute, the default is `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> We recommend against setting **runOnStartup** to `true` in production. Using this setting makes code execute at highly unpredictable times. In certain production settings, these extra executions can result in significantly higher costs for apps hosted in Consumption plans. For example, with **runOnStartup** enabled the trigger is invoked whenever your function app is scaled. Make sure you fully understand the production behavior of your functions before enabling **runOnStartup** in production.

## Usage

When a timer trigger function is invoked, a timer object is passed into the function. The following JSON is an example representation of the timer object.

```json
{
    "schedule":{
    },
    "scheduleStatus": {
        "last":"2016-10-04T10:15:00+00:00",
        "lastUpdated":"2016-10-04T10:16:00+00:00",
        "next":"2016-10-04T10:20:00+00:00"
    },
    "isPastDue":false
}
```

The `isPastDue` property is `true` when the current function invocation is later than scheduled. For example, a function app restart might cause an invocation to be missed.

## NCRONTAB expressions

Azure Functions uses the [NCronTab](https://github.com/atifaziz/NCrontab) library to interpret NCRONTAB expressions. An NCRONTAB expression is similar to a CRON expression except that it includes an additional sixth field at the beginning to use for time precision in seconds:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Each field can have one of the following types of values:

|Type  |Example  |When triggered  |
|---------|---------|---------|
|A specific value |<nobr>`0 5 * * * *`</nobr>| Once every hour of the day at minute 5 of each hour |
|All values (`*`)|<nobr>`0 * 5 * * *`</nobr>| At every minute in the hour, beginning at hour 5 |
|A range (`-` operator)|<nobr>`5-7 * * * * *`</nobr>| Three times a minute - at seconds 5 through 7 during every minute of every hour of each day |
|A set of values (`,` operator)|<nobr>`5,8,10 * * * * *`</nobr>| Three times a minute - at seconds 5, 8, and 10 during every minute of every hour of each day |
|An interval value (`/` operator)|<nobr>`0 */5 * * * *`</nobr>| 12 times an hour - at second 0 of every 5th minute of every hour of each day |

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### NCRONTAB examples

Here are some examples of NCRONTAB expressions you can use for the timer trigger in Azure Functions.

| Example            | When triggered                     |
|--------------------|------------------------------------|
| `0 */5 * * * *`    | once every five minutes            |
| `0 0 * * * *`      | once at the top of every hour      |
| `0 0 */2 * * *`    | once every two hours               |
| `0 0 9-17 * * *`   | once every hour from 9 AM to 5 PM  |
| `0 30 9 * * *`     | at 9:30 AM every day               |
| `0 30 9 * * 1-5`   | at 9:30 AM every weekday           |
| `0 30 9 * Jan Mon` | at 9:30 AM every Monday in January |

> [!NOTE]
> NCRONTAB expression require a **six field** format. The sixth field position is a value for seconds which is placed at the beginning of the expression. Five field cron expressions are not supported in Azure.

### NCRONTAB time zones

The numbers in a CRON expression refer to a time and date, not a time span. For example, a 5 in the `hour` field refers to 5:00 AM, not every 5 hours.

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## TimeSpan

 A `TimeSpan` can be used only for a function app that runs on an App Service Plan.

Unlike a CRON expression, a `TimeSpan` value specifies the time interval between each function invocation. When a function completes after running longer than the specified interval, the timer immediately invokes the function again.

Expressed as a string, the `TimeSpan` format is `hh:mm:ss` when `hh` is less than 24. When the first two digits are 24 or greater, the format is `dd:hh:mm`. Here are some examples:

| Example      | When triggered |
|--------------|----------------|
| "01:00:00"   | every hour     |
| "00:01:00"   | every minute   |
| "24:00:00"   | every 24 days  |
| "1.00:00:00" | every day      |

## Scale-out

If a function app scales out to multiple instances, only a single instance of a timer-triggered function is run across all instances.

## Function apps sharing Storage

If you are sharing storage accounts across function apps that are not deployed to app service, you might need to explicitly assign host ID to each app.

| Functions version | Setting                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (and higher)  | `AzureFunctionsWebHost__hostid` environment variable |
| 1.x               | `id` in *host.json*                                  |

You can omit the identifying value or manually set each function app's identifying configuration to a different value.

The timer trigger uses a storage lock to ensure that there is only one timer instance when a function app scales out to multiple instances. If two function apps share the same identifying configuration and each uses a timer trigger, only one timer runs.

## Retry behavior

Unlike the queue trigger, the timer trigger doesn't retry after a function fails. When a function fails, it isn't called again until the next time on the schedule.

## Troubleshooting

For information about what to do when the timer trigger doesn't work as expected, see [Investigating and reporting issues with timer triggered functions not firing](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## Next steps

> [!div class="nextstepaction"]
> [Go to a quickstart that uses a timer trigger](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Learn more about Azure functions triggers and bindings](functions-triggers-bindings.md)
