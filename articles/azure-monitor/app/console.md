---
title: Azure Application Insights for Console Applications | Microsoft Docs
description: Monitor web applications for availability, performance and usage.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp

ms.reviewer: lmolkova
---

# Application Insights for .NET console applications

[Application Insights](./app-insights-overview.md) lets you monitor your web application for availability, performance, and usage.

You need a subscription with [Microsoft Azure](https://azure.com). Sign in with a Microsoft account, which you might have for Windows, Xbox Live, or other Microsoft cloud services. Your team might have an organizational subscription to Azure: ask the owner to add you to it using your Microsoft account.

> [!NOTE]
> It is *highly recommended* to use the [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) package and associated instructions from [here](./worker-service.md) for any Console Applications. This package targets [`NetStandard2.0`](/dotnet/standard/net-standard), and hence can be used in .NET Core 2.1 or higher, and .NET Framework 4.7.2 or higher.

## Getting started

> [!IMPORTANT]
> New Azure regions **require** the use of connection strings instead of instrumentation keys. [Connection string](./sdk-connection-string.md?tabs=net) identifies the resource that you want to associate your telemetry data with. It also allows you to modify the endpoints your resource will use as a destination for your telemetry. You will need to copy the connection string and add it to your application's code or to an environment variable.

* In the [Azure portal](https://portal.azure.com), [create an Application Insights resource](./create-new-resource.md). For application type, choose **General**.
* Take a copy of the Instrumentation Key. Find the key in the **Essentials** drop-down of the new resource you created.
* Install latest [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) package.
* Set the instrumentation key in your code before tracking any telemetry (or set APPINSIGHTS_INSTRUMENTATIONKEY environment variable). After that, you should be able to manually track telemetry and see it on the Azure portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Telemetry is not sent instantly. Telemetry items are batched and sent by the ApplicationInsights SDK. In Console apps, which exits right after calling `Track()` methods, telemetry may not be sent unless `Flush()` and `Sleep`/`Delay` is done before the app exits as shown in [full example](#full-example) later in this article. `Sleep` is not required if you are using `InMemoryChannel`. There is an active issue regarding the need for `Sleep` which is tracked here: [ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Install latest version of [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) package - it automatically tracks HTTP, SQL, or some other external dependency calls.

You may initialize and configure Application Insights from the code or using `ApplicationInsights.config` file. Make sure initialization happens as early as possible. 

> [!NOTE]
> Instructions referring to **ApplicationInsights.config** are only applicable to apps that are targeting the .NET Framework, and do not apply to .NET Core applications.

### Using config file

By default, Application Insights SDK looks for `ApplicationInsights.config` file in the working directory when `TelemetryConfiguration` is being created

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

You may also specify path to the config file.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

For more information, see [configuration file reference](configuration-with-applicationinsights-config.md).

You may get a full example of the config file by installing latest version of [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) package. Here is the **minimal** configuration for dependency collection that is equivalent to the code example.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### Configuring telemetry collection from code
> [!NOTE]
> Reading config file is not supported on .NET Core. You may consider using [Application Insights SDK for ASP.NET Core](./asp-net-core.md)

* During application start-up create and configure `DependencyTrackingTelemetryModule` instance - it must be singleton and must be preserved for application lifetime.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Add common telemetry initializers

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

If you created configuration with plain `TelemetryConfiguration()` constructor, you need to enable correlation support additionally. **It is not needed** if you read configuration from file, used `TelemetryConfiguration.CreateDefault()` or `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* You may also want to install and initialize Performance Counter collector module as described [here](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)


#### Full example

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## Next steps
* [Monitor dependencies](./asp-net-dependencies.md) to see if REST, SQL, or other external resources are slowing you down.
* [Use the API](./api-custom-events-metrics.md) to send your own events and metrics for a more detailed view of your app's performance and usage.

