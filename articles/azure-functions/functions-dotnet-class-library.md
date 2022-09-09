---
title: Azure Functions C# developer reference
description: Understand how to develop Azure Functions using C#.

ms.topic: conceptual
ms.date: 07/24/2020

---
# Azure Functions C# developer reference

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

This article is an introduction to developing Azure Functions by using C# in .NET class libraries.

Azure Functions supports C# and C# script programming languages. If you're looking for guidance on [using C# in the Azure portal](functions-create-function-app-portal.md), see [C# script (.csx) developer reference](functions-reference-csharp.md).

This article assumes that you've already read the following articles:

* [Azure Functions developers guide](functions-reference.md)
* [Azure Functions Visual Studio 2019 Tools](functions-develop-vs.md)

## Supported versions

Versions of the Functions runtime work with specific versions of .NET. The following table shows the highest level of .NET Core and .NET Framework and .NET Core that can be used with a specific version of Functions in your project. 

| Functions runtime version | Max .NET version |
| ---- | ---- |
| Functions 3.x | .NET Core 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.7 |

To learn more, see [Azure Functions runtime versions overview](functions-versions.md)

## Functions class library project

In Visual Studio, the **Azure Functions** project template creates a C# class library project that contains the following files:

* [host.json](functions-host-json.md) - stores configuration settings that affect all functions in the project when running locally or in Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) - stores app settings and connection strings that are used when running locally. This file contains secrets and isn't published to your function app in Azure. Instead, [add app settings to your function app](functions-develop-vs.md#function-app-settings).

When you build the project, a folder structure that looks like the following example is generated in the build output directory:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

This directory is what gets deployed to your function app in Azure. The binding extensions required in [version 2.x](functions-versions.md) of the Functions runtime are [added to the project as NuGet packages](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> The build process creates a *function.json* file for each function. This *function.json* file is not meant to be edited directly. You can't change binding configuration or disable the function by editing this file. To learn how to disable a function, see [How to disable functions](disable-function.md).


## Methods recognized as functions

In a class library, a function is a static method with a `FunctionName` and a trigger attribute, as shown in the following example:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

The `FunctionName` attribute marks the method as a function entry point. The name must be unique within a project, start with a letter and only contain letters, numbers, `_`, and `-`, up to 127 characters in length. Project templates often create a method named `Run`, but the method name can be any valid C# method name.

The trigger attribute specifies the trigger type and binds input data to a method parameter. The example function is triggered by a queue message, and the queue message is passed to the method in the `myQueueItem` parameter.

## Method signature parameters

The method signature may contain parameters other than the one used with the trigger attribute. Here are some of the additional parameters that you can include:

* [Input and output bindings](functions-triggers-bindings.md) marked as such by decorating them with attributes.  
* An `ILogger` or `TraceWriter` ([version 1.x-only](functions-versions.md#creating-1x-apps)) parameter for [logging](#logging).
* A `CancellationToken` parameter for [graceful shutdown](#cancellation-tokens).
* [Binding expressions](./functions-bindings-expressions-patterns.md) parameters to get trigger metadata.

The order of parameters in the function signature does not matter. For example, you can put trigger parameters before or after other bindings, and you can put the logger parameter before or after trigger or binding parameters.

### Output binding example

The following example modifies the preceding one by adding an output queue binding. The function writes the queue message that triggers the function to a new queue message in a different queue.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

The binding reference articles ([Storage queues](functions-bindings-storage-queue.md), for example) explain which parameter types you can use with trigger, input, or output binding attributes.

### Binding expressions example

The following code gets the name of the queue to monitor from an app setting, and it gets the queue message creation time in the `insertionTime` parameter.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## Autogenerated function.json

The build process creates a *function.json* file in a function folder in the build folder. As noted earlier, this file is not meant to be edited directly. You can't change binding configuration or disable the function by editing this file. 

The purpose of this file is to provide information to the scale controller to use for [scaling decisions on the Consumption plan](functions-scale.md#how-the-consumption-and-premium-plans-work). For this reason, the file only has trigger info, not input or output bindings.

The generated *function.json* file includes a `configurationSource` property that tells the runtime to use .NET attributes for bindings, rather than *function.json* configuration. Here's an example:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## Microsoft.NET.Sdk.Functions

The *function.json* file generation is performed by the NuGet package [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

The same package is used for both version 1.x and 2.x of the Functions runtime. The target framework is what differentiates a 1.x project from a 2.x project. Here are the relevant parts of *.csproj* files, showing different target frameworks and the same `Sdk` package:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Among the `Sdk` package dependencies are triggers and bindings. A 1.x project refers to 1.x triggers and bindings because those triggers and bindings target the .NET Framework, while 2.x triggers and bindings target .NET Core.

The `Sdk` package also depends on [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), and indirectly on [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). These dependencies make sure that your project uses the versions of those packages that work with the Functions runtime version that the project targets. For example, `Newtonsoft.Json` has version 11 for .NET Framework 4.6.1, but the Functions runtime that targets .NET Framework 4.6.1 is only compatible with `Newtonsoft.Json` 9.0.1. So your function code in that project also has to use `Newtonsoft.Json` 9.0.1.

The source code for `Microsoft.NET.Sdk.Functions` is available in the GitHub repo [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## Runtime version

Visual Studio uses the [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) to run Functions projects. The Core Tools is a command-line interface for the Functions runtime.

If you install the Core Tools by using npm, that doesn't affect the Core Tools version used by Visual Studio. For the Functions runtime version 1.x, Visual Studio stores Core Tools versions in *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* and uses the latest version stored there. For Functions 2.x, the Core Tools are included in the **Azure Functions and Web Jobs Tools** extension. For both 1.x and 2.x, you can see what version is being used in the console output when you run a Functions project:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## ReadyToRun

You can compile your function app as [ReadyToRun binaries](/dotnet/core/whats-new/dotnet-core-3-0#readytorun-images). ReadyToRun is a form of ahead-of-time compilation that can improve startup performance to help reduce the impact of [cold-start](functions-scale.md#cold-start) when running in a [Consumption plan](functions-scale.md#consumption-plan).

ReadyToRun is available in .NET 3.0 and requires [version 3.0 of the Azure Functions runtime](functions-versions.md).

To compile your project as ReadyToRun, update your project file by adding the `<PublishReadyToRun>` and `<RuntimeIdentifier>` elements. The following is the configuration for publishing to a Windows 32-bit function app.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.1</TargetFramework>
  <AzureFunctionsVersion>v3</AzureFunctionsVersion>
  <PublishReadyToRun>true</PublishReadyToRun>
  <RuntimeIdentifier>win-x86</RuntimeIdentifier>
</PropertyGroup>
```

> [!IMPORTANT]
> ReadyToRun currently doesn't support cross-compilation. You must build your app on the same platform as the deployment target. Also, pay attention to the "bitness" that is configured in your function app. For example, if your function app in Azure is Windows 64-bit, you must compile your app on Windows with `win-x64` as the [runtime identifier](/dotnet/core/rid-catalog).

You can also build your app with ReadyToRun from the command line. For more information, see the `-p:PublishReadyToRun=true` option in [`dotnet publish`](/dotnet/core/tools/dotnet-publish).

## Supported types for bindings

Each binding has its own supported types; for instance, a blob trigger attribute can be applied to a string parameter, a POCO parameter, a `CloudBlockBlob` parameter, or any of several other supported types. The [binding reference article for blob bindings](functions-bindings-storage-blob-trigger.md#usage) lists all supported parameter types. For more information, see [Triggers and bindings](functions-triggers-bindings.md) and the [binding reference docs for each binding type](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## Binding to method return value

You can use a method return value for an output binding, by applying the attribute to the method return value. For examples, see [Triggers and bindings](./functions-bindings-return-value.md). 

Use the return value only if a successful function execution always results in a return value to pass to the output binding. Otherwise, use `ICollector` or `IAsyncCollector`, as shown in the following section.

## Writing multiple output values

To write multiple values to an output binding, or if a successful function invocation might not result in anything to pass to the output binding, use the [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) or [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) types. These types are write-only collections that are written to the output binding when the method completes.

This example writes multiple queue messages into the same queue using `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## Logging

To log output to your streaming logs in C#, include an argument of type [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). We recommend that you name it `log`, as in the following example:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Avoid using `Console.Write` in Azure Functions. For more information, see [Write logs in C# functions](functions-monitoring.md#write-logs-in-c-functions) in the **Monitor Azure Functions** article.

## Async

To make a function [asynchronous](/dotnet/csharp/programming-guide/concepts/async/), use the `async` keyword and return a `Task` object.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

You can't use `out` parameters in async functions. For output bindings, use the [function return value](#binding-to-method-return-value) or a [collector object](#writing-multiple-output-values) instead.

## Cancellation tokens

A function can accept a [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parameter, which enables the operating system to notify your code when the function is about to be terminated. You can use this notification to make sure the function doesn't terminate unexpectedly in a way that leaves data in an inconsistent state.

The following example shows how to check for impending function termination.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## Environment variables

To get an environment variable or an app setting value, use `System.Environment.GetEnvironmentVariable`, as shown in the following code example:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

App settings can be read from environment variables both when developing locally and when running in Azure. When developing locally, app settings come from the `Values` collection in the *local.settings.json* file. In both environments, local and Azure, `GetEnvironmentVariable("<app setting name>")` retrieves the value of the named app setting. For instance, when you're running locally, "My Site Name" would be returned if your *local.settings.json* file contains `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

The [System.Configuration.ConfigurationManager.AppSettings](/dotnet/api/system.configuration.configurationmanager.appsettings) property is an alternative API for getting app setting values, but we recommend that you use `GetEnvironmentVariable` as shown here.

## Binding at runtime

In C# and other .NET languages, you can use an [imperative](https://en.wikipedia.org/wiki/Imperative_programming) binding pattern, as opposed to the [*declarative*](https://en.wikipedia.org/wiki/Declarative_programming) bindings in attributes. Imperative binding is useful when binding parameters need to be computed at runtime rather than design time. With this pattern, you can bind to supported input and output bindings on-the-fly in your function code.

Define an imperative binding as follows:

- **Do not** include an attribute in the function signature for your desired imperative bindings.
- Pass in an input parameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs)
  or [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use the following C# pattern to perform the data binding.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` is the .NET attribute that defines your binding, and `T` is an input or output type that's supported by that binding type. `T` cannot be an `out` parameter type (such as `out JObject`). For example, the Mobile Apps table output binding supports [six output types](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), but you can only use [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) or [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) with imperative binding.

### Single attribute example

The following example code creates a [Storage blob output binding](functions-bindings-storage-blob-output.md)
with blob path that's defined at run time, then writes a string to the blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)
defines the [Storage blob](functions-bindings-storage-blob.md) input or output binding, and
[TextWriter](/dotnet/api/system.io.textwriter) is a supported output binding type.

### Multiple attribute example

The preceding example gets the app setting for the function app's main Storage account connection string (which is `AzureWebJobsStorage`). You can specify a custom app setting to use for the Storage account by adding the
[StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)
and passing the attribute array into `BindAsync<T>()`. Use a `Binder` parameter, not `IBinder`.  For example:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## Triggers and bindings 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## Next steps

> [!div class="nextstepaction"]
> [Learn more about triggers and bindings](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Learn more about best practices for Azure Functions](functions-best-practices.md)
